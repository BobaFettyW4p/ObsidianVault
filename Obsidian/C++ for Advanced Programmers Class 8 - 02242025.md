`decltype` - gives you the type of an expression without calling the expression

# Error Handling #

- handling errors feels like the biggest part of turning a POC into production code
	- C uses error codes to detect errors
		- biggest issue: no one ever checks them
	- C++ relies on exceptions, which are impossible to ignore

## Exceptions have a pervasive impact on your code ##

- exceptions can cause clean up code to be missed
```
void old_fashioned_f() {  
X *x = new X("foo");  
x->g(h());  
delete x; // Oops! Skipped if g or h throws an exception  
}
```
- it's possible to fix this with RAII
```
void f() {  
auto x = make_unique<X>("foo");  
x->f(g()); // x cleaned up even if g or h throw an exception  
}
```
- number of other impacts exceptions can have

## Exceptions and destructors ##
- an object's destructor is always called at the end of its lifetime
- destructors of automatic duration objects are called during exception passing even though normal code is bypassed
	- this is what makes RAII work!
	- however...

## What if a destructor throws during exception processing? ##
```
struct X { ~X() {throw "X";} };  
void f() {  
auto x = make_unique<X>("foo");  
x->f(g());  
}
```
- if `g` throws an exception, the `x`'s destruction will call `X`'s destructor
	- this will throw an exception in the middle of processing `g`'s exception
		- this makes no sense, so the language runtime will immediately call `std::terminate()`
			- Best way to avoid this: don't throw exceptions in your destructor
				- if it may, be sure to catch before returning
```
struct X {  
~X() {  
try {  
g(*this); // g may throw  
} catch(...) {} // Ignoring may be only option  
}  
};
```

## Writing exception-safe interfaces ##
- when you write a function or method, you should think about what happens if an exception occurs while passing arguments
	- this only applies to pass-by-value
- in a traditional stack, `stack::pop()` returns a void (discarding the top element)
	- if you want to see the top element before you pop it, call `stack::top()`
	- why does `std::stack` work this way?
		- Exception safety!
			- you would expect `std::stack` to be able to pop an object of a stack and return its value
```
stack<A> stk;
...
A a(stk.pop()); //Illegal
```
- the issue arises when `A`'s copy or move constructor throws an exception
	- the top element could be lost forever
	- do this instead:
```
stack<A> stk;  
...  
A a(stk.top());  
stk.pop();
```

- if there is any chance a move throws, then you have to copy
- not knowing if an exception can be thrown can lead to inefficient algorithms being used for the purposes of safety
	- how do we grow a vector?
		- a vector stores a bunch of objects in a contiguous array it has reserved in memory
			- logically uses a `unique_ptr<int[]>` to manage memory
![[Pasted image 20250307202224.png]]
- suppose we call `push_back(2)` and there is no more room in the array
	- the vector will reserve a larger array(`make_unique<int[8]>()) and copy the `int`s over
![[Pasted image 20250307202306.png]]
- in this example, there's no need to move, but what if we have a vector of things that...
	- can't be copied
		- `vector<unique_ptr<int>>`
	- or are expensive to copy but cheap to move
		- `vecotr<HTMLPage>`
	- we'd prefer to move instead
		- what if an exception is thrown while moving the objects?
			- `push_back` will throw an exception, but it won't be recoverable because neither the old nor the new array will be usable
				- if `push_back`throws an exception, it's supposed to leave the vector untouched
![[Pasted image 20250307202553.png]]
- If there is any change a `move` throws, then we have to copy
	- performance may be worse
	- something like `vector<unique_ptr<T>>` would simply be impossible
		- unacceptable!

## `noexcept` ##
- writing code that can tolerate an exception at any conceivable moment is a drag at best and disastrous at worse
- a function or method can be labelled as `noexcept`, which means it will never throw an exception
	- `std::terminate` will be called if it tries
- if a type stored in a vector has a `noexcept` move constructor, then it moves
	- otherwise, it copies it
- `unique_ptrs` move constructor is annotated as `noexcept` so we can move them
	- important since we are not able to copy `unique_ptr`s
- there is a type trait called `is_nothrow_move_constructible_v` that can be used with SFINAE or  Concepts to call the right code
C++ used to have exception specifications
- removed in C++17
	- they were awful

## Templates and `noexcept` ##
```
template<typename T>  
auto square(T &&x) {  
return x*x;  
}
```
- this square template noexcept?
	- it depends on whether T's move constructor is noexcept
		- you can give `noexcept` a `true` or `false` argument to say so
```
template<typename T>  
auto square(T &&x)  
noexcept(is_nothrow_move_constructible_v<T>) {  
return x*x;  
}
```
- sometimes you don't have a convenient type trait
- there is also a function named `noexcept` that takes an expression returns True if it is `noexcept`
```
template<typename T>  
auto square(T &&x)  
noexcept(noexcept(x*x)  
&& is_nothrow_move_constructible_v<T>) {  
return x*x;  
}
```
- this is overkill for square, but may be needed sometimes
- `noexcept` is used for a lot of different things in C++
	- introducing a new keywork can break existing code

## `noexcept` and destructors ##
- destructors should almost always be `noexcept`
	- class destructors are implicitly `noexcept`
	- you have to explicitly say `noexcept(false)` if you want a destructor to be able to throw an exception

- if your classes move and copy constructors are `noexcept`, make sure to declare them that way
- don't try to declare everything with the correct `noexcept` specifier
	- don't hesitate if there's a benefit to do it though

- while exceptions are the norm, they're complicated
	- many systems disable exceptions
		- this breaks much of the standard library
	- C++23 added a new way to handle errors: `std::expected`

# Polymorphic containers #
- so far, all containers we have used only contain objects of one types
	- `vector<int>` holds vectors
	- `list<string>` holds strings
	- `map<string, int>` lets you look up the integer corresponding to a string
		- `m["foo"s] = 3;`
- we can use inheritance and virtual functions to put objects of different dynamic types in a container
```
vector<unique_ptr<Animal>> zoo;  
zoo.push_back(make_unique<Gorilla>());  
zoo.push_back(make_unique<Lobster>());
```

- there will be times when you want different static types
	- C++ includes data structures that can hold objects of different types

# `pair` and `tuple` #
- are to classes what lambdas are to functions
- create an anonymous class on the fly

## `tuple` ## 
- anonymous struct with fields of given types
	- same relation to structs as lambdas do to functions
	- can user `get<>` to access the fields
```
tuple<string, int, double> si("str", 2, 3.5);  
tuple di(2.5, 3, 'c');  
// CTAD: di will be a tuple<double,int, char>  
cout << get<0>(di) // prints 2.5  
cout << get<char>(di); // prints 'c'  
int three = get<1>(di);
```

## `pair` ##
- pairs are like tuples that always have two types
	- `pair<int, string> = { 1, "foo"s };`
- only difference is members also have names
	- `p.first` is effectively a synonym for `get<0>(p)`
	- `p.second` is effectively a synonym for `get<1>(p)`

- one natural use for pair and tuple is to let functions return multiple values
```
pair<int, int> f() {  
return {1, 2}; // ok  
}

tuple<int, int, char> g() {  
return {1, 2, 'u'}; // OK starting in C++17  
}
```


- there aren't many reasons to use pairs now that tuples exist
	- simply use a tuple of size 2
```
map<int, string> m = {{1, "foo"}, {0, "bar"}};  
for(auto &kv: m) { // kv is a pair<int, string>  
cout << format("key: {}\tvalue: {}", p.first, get<1>(p);
```

# Structured Bindings #
- work very well with functions that return multiple values in a pair or tuple
	- suppose you want to get several values back from a function
		- use a `tuple` or `pair`
			- they can be clumsy to work with
			- how do you get them into individual variables after you've called the function?
				- `std::tie` is the old way
```
tuple<string, Rank, long> identity(Person);  
string name;  
Rank rank;  
tie(name, rank, std::ignore) = identity(Mike);
```
## `std::tie` ##
- it can only assign to existing variables
- it does not deduce types
- it can't decompose types other than tuple/pair
```
int a[2] = {1,2};  
auto [x,y] = a; // creates e[2], copies a into e, then x refers to e[0], y refers to e[1]  
auto& [xr, yr] = a; // xr refers to a[0], yr refers to a[1]
float x{}; char y{}; int z{};  
tuple<float&,char&&,int> tpl(x,std::move(y),z);  
const auto& [a,b,c] = tpl;  
// a names a structured binding of type float& that refers to x  
// b names a structured binding of type char&& that refers to y  
// c names a structured binding of type const int that refers to the 3rd element of tpl
struct S { int x1 : 2; volatile double y1; };  
S f();  
const auto [x, y] = f(); // x is a const int lvalue identifying the 2-bit bit field  
// y is a const volatile double lvalue
```
- more realistic example
	- suppose we have a function that returns either a web page or a network error code:
```
pair<unique_ptr<Page>, int> getWebPage() {  
/* ... */  
if(succeeded)  
return { page, status_code /* 200 */ };  
else  
return { unique_ptr<Page>, errCode };  
}  
/* ... */  
auto [ page, status] = getWebPage();  
if(status == 200) processPage(page)  
else if (status == 404) { cout << "Not found" << endl; }  
else { cout << format("Unknown error {}\n", status); }
```

## `std::optional` ##
- sometimes it's useful for an object to be unset
	- like None in python
	- pointers can be `null`, but don't want to use if we don't have to
```
// f may fail or return an int  
optional<int> f()  
{ return /* succeeded */ ? 1 : {} }  
auto r = f();  
if(r) { // f return a result  
cout << "succeeded: "  
<< r.value() << endl;  
} else {  
cout << "failed" << endl;  
}
```

## `std::variant` ##
- C++ has a lightweight "variant" abstraction that generalizes unions
	- `A variant<A,B>` holds either an A or a B
	- variants are the basis of an approach to OO that will
		- often have much better performance than virtuals
		- more dynamic than our `Animal` concept
			- variants enable a polymorphic paradigm with runtime dispatch
		- everything is value based
			- no need for references, `unique_ptr`, RAII
		- great support for Open-Closed extensibility
- basic use:
	- think of a variant like a `tuple`, but instead of holding all of its fields at once, it only contains one of them at a time
```
variant<int, double> v = 3; // Holds int  
get<0>(v); // Returns 3  
get<1>(v); // Throws std::bad_variant_access  
v = 3.5; // Now holds a double  
get<double>(v); // Returns 3.5
```
- you can also check what is in it:
```
v.index; // returns 1  
holds_alternative<double>(v); // returns true  
holds_alternative<int>(v); // returns false
```

# "OO" Design #
- C++ offers many ways to address the sort of problems covered by OO design
	- not all of these are OO techniques

## Templates and OO can solve similar problems ##
>Generic programming should "just" be normal programming
> -- Bjarne Strousroup

## Using templates instead of inheritance and virtuals ##
- new C++ features, like C++20 `concepts`, were intentionally designed to be familiar for replacing object-oriented code
- OO and templates can solve similar problems
	- imagine the following OO code:
```
struct Animal {  
virtual string name() = 0;  
virtual string eats() = 0;  
};  
class Cat : public Animal {  
string name() override { return "cat"; }  
string eats() override { return "delicious mice"; }  
};  
// More animals...  
int main() {  
unique_ptr<Animal> a = make_unique<Cat>();  
cout << "A " << a->name() << " eats " << a->eats();  
}
```
- do we have to use OO?
	- not really:
```
struct Cat {  
string eats() { return "delicious mice"; }  
string name() { return "cat";}  
};  
// More animals...  
int main() {  
auto a = Cat();  
cout << "A " << a.name() << " eats " << a.eats();  
}
```
- this is much simpler
	- but we lost the understanding that `a` is an animal
		- `a` could have the type `House` or `int` and we might not find out until much later when something depends on `a` being an animal goes wrong
		- we need a way to codify our expectations for `a` without all of the overhead and complexity of creating a base class

## Concepts ##

- Concepts play the analogous role for generic programming that base classes do in OOP
	- a concept explains what operations a type supports
	- the following concpet encapsulates the same info as the base `Animal` class from above:
```
template<typename T>  
concept Animal = requires(T a) {  
{ a.eats() } -> convertible_to<string>;  
{ a.name() } -> convertible_to<string>;  
};
```
- now we can ensure `a` represents an `Animal`
	- with the above concept, we can specify that `a` must satisfy the `Animal` concept
		- the compiler will enforce this
```
int main() {  
Animal auto a = Cat();  
cout << "A " << a.name() << " eats " << a.eats();  
}
```

- usage is almost identical to before
```
Animal auto a = Cat();  
cout << "A " << a.name()  
<< " eats " << a.eats();
```
### How does this compare to the OO version? ###
- performance is better
	- objects are created on the stack
	- no virtual dispatch
- no inheritance
	- easier to adapt classes to our code
		- no risk of "spaghetti inheritance"
		- however, it weakens type safety
- no runtime polymorphism
	- the following is legal is `Animal` is a class but not if it is a concept
		- `set<unique_ptr<Animal>> zoo;`

# if C++ didn't have mutexes, how would we design them? #
- this is how Java does it
	- utilizes inheritance and virtual methods
```
struct lockable {  
virtual void lock() = 0;  
virtual void unlock() = 0;  
};  
struct mutex: public lockable {  
void lock() override;  
void unlock() override;  
};  
struct lock_guard {  
lock_guard(lockable &m) : m(m) { m.lock(); }  
~lock_guard() { m.unlock(); }  
lockable &m;  
};
```

- using our Java-style mutex:
```
mutex m;  
void f() {  
lock_guard lk(m);  
// do stuff  
}
```
- this differs from C++ style mutexes
	- C++ mutexes do not inherit from a lockable base class
		- the C++ committee decided to use templates instead of the virtual override approach taken by Java
			- since mutexes are frequently used in performance-critical code, this is undoubtedly the correct choice
- C++ style mutexes:
```
struct mutex {  
void lock();  
void unlock();  
};  
template<typename T>  
struct lock_guard {  
lock_guard(T &m) : m(m) { m.lock(); }  
~lock_guard() { m.unlock(); }  
T &m;  
};
```
- this is how we use our C++ style mutex
```
// Exactly the same as before!  
mutex m;  
void f() {  
lock_guard lk(m);  
// do stuff  
}
```

# Class Template Argument Deduction (CTAD) #
- the following line depended on CTAD
	- `lock_guard lk(m);`
	- CTAD infers the template arguments for `lock_guard<mutex>` from the constructor
		- similar to how Function Template Argument Deduction infers the template arguments for function templates
			- CTAD can often be useful when using templates instead of virtuals
- if `tp1` and `tp2` are of type `time_point<C, duration<R>>`
	- `duration d = tp1 - tp2; // duration<R>`

- there is usually a choice between base classes/virtuals and templates/concepts
	- C++ supports many approaches for "object-orientation"
		- we need an understanding of OO design best practices

# Using duck-typed variants for performant, extensible OO #
>"If it walks like a duck and quacks like a duck, then it's a duck
- can we apply this to types?
## Inheritance models "isA" ##
- inheritance is a model of the "isA" concept
	- duck typing gives a different notion of "isA"
		- if a class has a `walk()` method and a `quack()` method, let's not worry about inheritance
			- just call it a duck

## Templates use duck typing ##
- `T square(auto x) { return x*x; }`
- `square(5); // OK`
- we don't require that the type of `x` inherits from a `HasMultiplication` class, simply that `operator*` makes sense to use here

## Concepts ##
- C++20 Concepts improve duck typing
	- we could have a `HasMultiplication` concept that would do the trick without requiring any complex inheritance hierarchies

## Dynamic dispatch ##
- while C++ templates have always been duck typed, templates are used for compile-time dispatch
	- in contrast, OO is used for dynamic dispatch
- since duck typing is flexible and forgiving while remaining statically typesafe, people have asked whether we could use dynamically-dispatched duck typing as an alternative to inheritance

## Using duck typed variants in place of OO ##
- suppose we knew (at compile time) all of the `Animal` class
	- but we don't know the type of a particular animal until run-time
		- instead of inheriting from an abstract animal base class, we can have an animal variant
			- `using Animal = variant<Cat, Dog>;`

## How do I call a method on a variant? ##
- the C++ standard library has a solution: `std::visit`
	- if `v` is a variant, and `c` is a callable, `visit(v, c)` calls `c` with whatever is stored in `v` as its argument
		- does this solve our problem of making variants behave like virtuals?
```
Animal a = Cat(); // a is a cat
cout << visit(
[](auto &x) { x.name(); }, a);
)
```
- this prints "Cat", exactly like we would want
- here's a running example:
	- `visit([](auto const &a) -> string {return a.name(); }, c)`
### How does it compare to using virtuals or templates? ###
- almost as fast as templates
	- since `Animal` is a single type that can hold either a `Cat` or a `Dog`, we can just use animals by value instead of having to do memory allocations
- as dynamic as traditional OO
	- A `set<animal>` works great
		- unlike our concepts version

## Malleable typing ##
- virtual functions need to exactly match what they override, so we could give `Cat`'s `eat()` a defaulted argument
```
struct Cat : public Animal {  
void eat(string prey = "mouse") override; // ill-formed
```
- this isn't a problem for variants
- as long as `eats()` is callable, we don't care about the rest:
```
struct Cat {  
void eat(string prey = "mouse");  
};  
visit([](auto &x) { x.eats(); }, a); // OK. Eats a mouse
```
- just as we discussed with the Visitor Pattern, users of the Animal type can add their own methods
	- will utilize the "overloaded pattern"

## The overloaded pattern ##
- define an `overloaded` class (only need to do this once)
- `template<class... Ts>`
- `struct overloaded : Ts... { using Ts::operator()...; };`
	- this inherits the function call operator of everything it is constructed with
	- we should make this clear by creating a `lifeSpan()` method like we did before:
```
overloaded lifeSpan([](Cat &) { return 12 },  
[](Dog &) { return 10});  
// Get life span without knowing whether  
// a is a Cat or a Dog  
cout << visit(lifeSpan, a); // Prints 12
```
- Note: this idiom relies on CTAD and aggregates deducing the template arguments for you

## Problems with duck typing ##
- the notation is much uglier than calling a virtual function
- while the flexibility is nice, duck typing reduces type safety
	- it cannot tell that a Shape's `draw()` method for drawing a picture is different than a Cowboy's `draw()` method for drawing a gun
- variants always use as much space as the biggest type
- whenever we create a new kind of Animal, we have to add it to the variant, which can cause maintenance problems
- a best practice is to only prefer variant-based polymorphism over virtuals or templates when there is a clear benefit
	- you'll still use it a lot, but less than virtual functions or templates
