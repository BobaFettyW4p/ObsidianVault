- hardware concurrency v software concurrency
	- 100 threads with only one core, then it means the threads have to share the same core, one at a time
		- not actually getting concurrency
- don't use `std::ostringstream`, use `std::format`
- if you don't want to join a thread, but want it to run to completion, you `.detach()` it

# Distributed counter #
- a counter that can be incremented or read by any thread
	- seems simple, has a lot of complexity
- you can make a `shared_mutex` to keep different `DistributedCounter` objects from interfering with each other
	- need to declare them with the `mutable` keywork:
		- `shared_mutex mutable mtx;`
## logical constness ##
- would like `DistributedCounter::get()` to be `const`
	- seems illogical that simply reading the current value modifies the object
		- however, getting the reader lock, modifies the state of the `mtx` member of the counter object
			- thus, we label it `mutable`
				- we don't want changes to `mtx` to be changes to the state modeled by the counter
## Amdahl's law ##
- the more sequential tasks in a program, the less it benefits from parallelism
![[Pasted image 20250306193445.png]]


# Cache Conscious Programming #
- accessing main memory can take processors hundreds of cycles
- processors use high-speed caches to maintain local copies of data
- if another processor needs to R/W that memory, other processors have to flush or invalidate cached copies of that memory

## Cache lines and false sharing ##
- when memory is moved from memory to cache, enough data is always moved to fill a "cache line"
	- this varies greatly by processor, but is typically 32 bytes
	- if 2 processors are modifying data within 32 bytes (or whatever the cache line is), they are constantly forcing each other to invalidate their cache
		- this is known as "false sharing"
### How can we eliminate false sharing? ###
- independent objects must reside on independent cache lines
	- a single memory location can only be mapped to a single cache line
	- this is typically accomplished by adding padding between objects
## Direct-Mapped Caches ##
- often a single memory location can only be mapped to one or two possible cache lines
	- not understanding this can have severe consequences...

### Ghostscript ###
- ghostscript is a popular postscript rendering program written by Peter Deutsch
	- `malloc()/free()` performance is critical in postscript
		- luckily, Peter Deutsch was a memory management expert
	- ghostscript used a custom allocator that stored free pages in a linked list
	- 10 years later, ghostscript's memory manager was actually slowing processing by 30%
		- Why?
			- the custom allocator maintained a pool of free pages in a linked list, with the first word of each free page as a pointer to the next
				- since ghostscript was developed on a machine without a direct-mapped cache, it ran fine
				- when it was utilized on a machine with direct-mapped caches, all the freelist pointers mapped to the same cache line
				- ghostscript was spending about 1/3 of its time in cache misses

## How does C++ handle cache-conscious programming? ##
- you can make an educated guess, but that's brittle
	- if you guess the wrong amount, it might not work
- C++17 introduced a more portable approach
	- `std::hardware_destructive_interference_size`
		- if objects are further than it, they will be placed on different lines; otherwise they "should" end up on the same line
	- `std::hardware_constructive_interference_size`
		- if objects are closer than it, they "should" end up on the same cache line

## Cache-Conscious Programming ##
- objects or fields that are accessed independently should be aligned and padded so they end up on different cache lines
- keep read-only data separate from data that is modified frequently
- When possible, split an object into thread-local pieces
	- a counter used for statistics could be split into an array of counters, one per thread, each on a different cache line
	- The split counter allows each thread to update its own replica without causing cache coherence traffic
- If a lock protects frequently-modified data, keep the lock and data on distinct cache lines, so threads trying to acquire the lock do not interfere with access to the data
- If a lock protects uncontended data, try and keep the lock and the data on the same cache lines, so acquiring the lock will load data into the cache
- If a struct contains a large chunk of data whose size is divisible by a high power or two, consider separating it out of the class and holding it with a `unique_ptr` to avoid Ghostscript problem
- use a profiling tool like VTune to identify cache bottlenecks

# CTAD #
- Class Template Argument Deduction
	- you can use `lock_guard l(mtx);` instead of the more clumsy `lock_guard<mutex> l(mtx);`
	- usually with classes, you have to specify the template arguments, as in `Matrix<double,3>`
- for functions, specifying the template parameter is optional
	- `auto square(auto x) { return x*x; }`
		- you're able to use `cout << f(7);` instead of the more clumsy `cout<< f<int>(7);` (this still works)
	- you are able to let the compiler automatically deduce the type unless you want something other than what would be deduced

- CTAD works in the same way
	- since creating an object calls the constructor, the compiler can deduce template arguments passed to the constructor:
```
template<class MutexType>  
struct lock_guard {  
lock_guard(MutexType &m) : m(m) {  
m.lock();  
}  
/* ... */  
MutexType &m;  
};  
std::mutex m;  
lock_guard l(m); // MutexType must be std::mutex
```
- this also lets us write `vecotr v = {1,2,3}; //vector<int>`

- CTAD has advanced features like deduction guides
	- not germane to this class

# Move semantics #
- How to pass an argument
	- pass by value
		- `void f(X x);`
	- pass by reference
		- `void f(X &x);`
	- pass by move
		- `void f(X &&x);`
- most languages use one by default, C++ has all three and (makes|lets) you choose which

## What does it mean to move an object ##
- moving `y` to `x`
	- `x = move(y);`
	- this means that the value of `x` is the same as the value `y` started at
- this can be done with ordinary copying
	- `x = y;`
- the difference between a copy and a move is that `y` is allowed to be changed by the move
	- subsequence code should not use the value of `y`

- since a move can raid the old object for parts, it's more efficient
	- moving a tree only requires moving the root
![[Pasted image 20250306202749.png]]


### Semantic argument to move rather than copy ###
- some classes don't make sense to copy and therefore have deleted copy constructors
```
struct X {  
X(X const &) = delete;  
/* ... */  
};
```

- if you copy a `unique_ptr`, the managed object would be owned by two `unique_ptrs`
	- defeats the purpose of `unique_ptrs` and stops RAII
	- using a `move` instead fixes this
- objects are often much cheaper to "move" than copy
	- some objects (`unique_ptr`) can be moved but not copied
- C++ lets you use `...` to indicate a "pack" of template parameters

- How does C++ know whether to move or copy?
	- if it is sure the old object doesn't need the value anymore
	- if the old object is a temporary value and has no name
		- so it can't be referred to again
		- `auto x = make_unique<X>(); // x owns the X object`
	- the only other way is if `std::move` is used to indicate it's okay to move from
		- `auto y = move(x); // now y owns the x object`

### How does move work ###
- Rvalue references
	- A reference with `&&` instead of just `&` can bind to a temporary value and move it elsewhere
	- objects are often much cheaper to "move" than copy
		- for example, deep copying a tree
		- some objects (`unique_ptr`) can be moved but not copied
```
template<class T>  
void swap(T& a, T& b)// "perfect swap"(almost)  
{  
T tmp = move(a); // could invalidate a  
a = move(b); // could invalidate b  
b = move(tmp); // could invalidate tmp  
}
```

#### How do I make a type movable? ####
- you don't, it is a performance optimization because C++ will just copy if there are no move operations
	- if moving is more efficient, you should create move constructors and move assignment operators
```
template<class T> class vector {  
// ...  
vector(vector<T> const &); // copy constructor  
vector(vector<T> &&); // move constructor  
vector& operator=(const vector<T>&); // copy assignment  
vector& operator=(vector<T>&&); // move assignment };
```

- sometimes, the compiler will automatically generate move constructors and move assignments operators that just move all the members
- if you want to force the compiler to generate the default move constructor when it wouldn't normally, you can force that with `= default`
```
struct S {  
S(S const &); // OK, but stops move constructor generation  
S(S &&) = default; // Gets it back  
/* ... */  
};
```

## Summary: Three ways of copying ##
- by value
	- the function gets its own copy
		- `void f(int i) { i += 2; }`
- by lvalue reference
	- the function works with the caller's object
		- `void f(int &i) { i+= 2; }`
- by rvalue reference
	- if the function is called on a temporary
		- `void f(X &&x) { ... }`

## The Rule of Five ##
- discussion to expand the rule of three to the rule of 5
	- if you define any of...
		- destructor
		- copy constructor
		- copy assignment operator
		- move constructor
		- move assignment operator
	- you should review all of them to ensure they perform the correct operations
		- you may need to define them
		- they are all related

## **How is `std::move` implemented? ##
- yet another way of "argument passing"
- if `&&` is used in a template argument, it means "forwarding reference", which binds to an rvalue reference or a regular lvalue reference
- Template type deduction takes place according to the following "collapsing rules" which are only applied in templates
	![[Pasted image 20250306204107.png]]
# Perfect forwarding #
- a lot of times you want to wrap a function and pass your arguments to it
	- "every problem in programming can be solved with another layer of indirection"
- this is surprisingly difficult because you have to properly forward all the different ways of passing parameters

## A naive `make_unique` implementation ##
```
template<typename T, typename Arg>  
unique_ptr<T> makeUnique(Arg arg)  
{  
return unique_ptr<T>(new T(arg));  
}
```
- this code assumes that T takes one constructor argument
	- it could take one or fewer
### Our first improvement: Variadics ###
- C++ lets you use `...` to indicate a "pack" of template parameters
	- basic use is straightforward:
```
template<typename T, typename... Arg>  
unique_ptr<T> makeUnique(Arg... arg)  
{  
return unique_ptr<T>(new T(arg...));  
}
```
- what's wrong?
```
struct X {  
// This constructor modifies its argument  
X(int &i) { i++; }  
};
//^^ modifies a copy of it's argument
```

```
int i = 2;
auto xp = makeUnique<X>(i); // i is still 2
```
- since `makeUnique` inferred that `Args...` is `int` it passed `i` by value
### What if we took our arguments by reference? ###
```
template<typename T, typename... Args>  
unique_ptr<T> makeUnique(Args &... args)  
{  
return unique_ptr<T>(new T(args ...));  
}

int i= 2;
auto xp = makeUnique<X>(i); // i is 3
//this works!
```

- can't bind a `T &` to an rvalue, so if I call `make_unique` with any rvalue arguments then I will get a compile error
```
struct X { X(int const &); };  
new X(2); // Legal  
// We would like the following line  
// to also be able to create an X(2), but..  
makeUnique<X>(2); // Ill-formed!
```
- our current argument list for `make_unique` tries to bind to 2 with an `int &` but it doesn't make sense to refer to the constant numerical value 2 with an `int &` because a non-const reference could try and change the value of 2
	- this can get very ugly vey fast
		- workaround is to utilize `std::forward`
```
// Perfectly forwards everything  
template<typename T, typename... Args>  
unique_ptr<T> makeUnique(Args &&... args)  
{  
return unique_ptr<T>(new T(forward<Args>(args)...));  
}
```
- how does it work?
	- consists of two function templates:
```
template< class T >  
constexpr T&& forward(remove_reference_t<T>& t )  
{ return t; }  
template< class T >  
constexpr T&& forward(remove_reference_t<T>&& t )  
{ return t; }
```

# Low-level and Systems Programming #
- Pointers
	- low level way to refer to a location in physical memory
		- for the most part, one should use references and higher-level lightweight abstractions like `unique_ptr`
		- some cases aren't covered by `unique_ptr`:
			- a reference always refers to the same object, but doesn't own it - can't be "unbound"
			- a `unique_ptr` can  change what it manages, and can be unset, but it owns any object it manages
			- what if we want an object to be unsettable but not owned
				- currently not possible
				- `observer_ptr` is a possible future solution

## C++ sometimes forces you to use a pointer ##
- `this` is a pointer
	- better to use `*this`
- `main(int argc, char **argv)` uses pointers to pointers to pass command line arguments
	- this is to maintain C compatibility

### When do we need pointers? ###
- low level programming
	- C++ is a systems programming language
		- may need to access memory directly
- Advanced memory management
	- nearly all can be handled with `unique_ptr` and `shared_ptr`
	- there are situations they don't cover, which is where raw pointers come in

### Pointers to a type contain the address of an object of the given type ###
`A *ap = new A();`
- `ap` contains the address of an object of type `A` in memory
- `new A()` constructs an object of type `A` in memory and returns its address
- `new A()` has the same relation to `A *` as `make_unique<A>()` has to `unique_ptr<A>`
- `A a; ap = &a; // & is the addressof operator`

### Pointers can be dereferenced with `*` ###
	- `A a = *ap;`
		- `->` is an abbreviation for `(*_)`
		- `ap->foo(); //Same as (*ap).foo()`
- raw pointers do not have destructors, and do not manage the lifetime of the object it points to
	- If a pointer is not pointing to any object, you should make sure it is `nullptr`
```
ap = nullptr; // don’t point at anything  
if(ap) { ap->foo(); } // foo won’t be called
```
- the type of `nullptr` is `nullptr_t`
	- this implicitly converts to any pointer type

- Old C/C++ code uses `0` or `NULL` to represent an empty pointer
	- don't do this, breaks overloading
```
nt f(char *);  
int f(int);  
auto x = f(0); // Which f was meant?
```
- Solution: don't use `0` for `nullptr`
```
auto x = f(0); // Calls f(int)  
auto x = f(nullptr); // Calls f(char *)
```

### Pointer arithmetic ### 
- technically possible:
```
// Arrays decay to a pointer to the 0th element  
A *aap = new A[10];  
(*aap)[5] == *(aap + 5) // 5th elt of array
```
- this doesn't add 5 to the address, but adds enough to get the 5th element, starting at 0
- generally try to avoid this; it's better to use lightweight abstractions like `std::array` or `std::vector`

## C Strings ##
- C-style string literals give you pointers
- `"foo"` is actually a literal of type `char const[4]`
```
auto cp = "foo"; // c is a char const *  
cout << cp[1]; // prints o
```
- not nearly as powerful or typesafe as C++ strings, which have many methods
	- a good habit to use C++-string literals
		- `auto s = "foo"s; // s is a std::string`
### `string_view` ###
- There are a lot of ways to express text in C++
	- `std::string`
	- A C string( `char const *`)
	- A pointer and a length
	- all of these are possible, but some of these are clumsy and inefficient
```
void f(string const &);  
f(string(foo)); // OK  
f("foo"s); // OK. Same with user-defined literal  
f("foo"); // Has to copy text into string  
f({"foobar", 3}); // Has to copy text into string
```
- this works for short strings, what if they're longer?
- this is where `string_view` comes in
	- a `string_view` wraps a `char const *` and a length
```
void f(string_view);  
f(string(foo)); // OK. Views buffer  
f("foo"s); // OK.  
f("foo"); // OK. No more copying text  
f({"foobar", 3}); // OK. No more copying text
```
- the hope is that functions that now take a `string const &` or a `const char *` will take a `string_view` instead
- it's easy to go from a `string` to a `string_view`, it can be hard to go the other way around
```
string s("foo");  
string_view sv(s); // OK  
string s2(sv); // OK: string_view to string  
string s3 = sv; // Ill-formed!
```
- What's wrong?
	- the `string` constructor for `s3` is `explicit`, so it doesn't generate and implicit type conversion
		- this doesn't sound so bad
			- biggest issue is that function arguments in C++ use copy initialization
```
void f(string const &s);  
string_view sv("foo");  
f(sv); // Ill-formed! Copy initialization
```
- Solution: `f` should take a `string_view`

### Raw string literals ###
- when you have a long string, is sometimes painful to constantly escape internal quotations and backslashes
	- a quoted string cannot extend across multiple lines
	- general form is `R"delim(text)delim"`
		- `delim` is whatever you want; use it to avoid collisions:
```
string s = R"foo(Hello  
(Raw String) Literals)foo";
```
- delimiter can also be skipped:
```
string code =  
R"(#include<iostream>  
using namespace std;  
int main()  
{ cout << "Hello, world\n";  
})";
```

## pointers to functions ##
- usually describe a type by how it is used:
```
int *ip; // Means *ip is an int
int (*fp)(int, int); // *fp can be called with 2 ints
```
- `fp` in action:
```
int f(int i,int j) { ... }  
fp = &f;  
fp(2, 3);  
// The following line only works without captures  
fp = [](int i, int j) { return i + j; }
```

- sometimes we don't know what function we want to call until runtime:
```
double mean(vector<double> const &) {...}  
double median(vector<double> const &) { ... }  
cout << "Should I use means or medians ";  
string answer;  
cin >> answer;  
double (*averager)(vector<double> const &)  
= (answer == "mean" ? mean : median);  
cout << "The average home price is ";  
cout << averager(getHomePrices()) << endl;
```

- Pointers to members
```
struct A {  
int i;  
int j;  
void foo(double);  
void bar(double);  
};
```
## We would like to be able to point to a particular member of `A` ##
- not an address because we haven't specified an `A` object
	- more like an offset into `A` objects:
```
int A::*aip = &A::i;  
void (A::*afp)(double) = &A::foo;  
A *ap = new A;  
A a;  
ap->*aip = 3; // Set ap->i to 3  
(a.*afp)(3.141592); // Calls a.foo(3.141592)
```

## Pointers to member functions ##
```
vector<Animal *> zoo;  
zoo.push_back(new Elephant);  
zoo.push_back(new Zebra);  
zoo.push_back(new Bear);  
cout << "Feeding time (f) or Bedtime (b)?"  
char c;  
cin >> c;  
void (Animal::*ap)()  
= c == 'f' ? &Animal::eat : &Animal::sleep;  
for(auto animal : zoo) {  
animal->*ap();  
}
```
- since `unique_ptr` and `shared_ptr` don't overload `operator->*()`, if we want to make the previous example delete objects when the zoo closes (or when there's an exception when constructing an animal), we should modify it as follows:
```
vector<unique_ptr<Animal>> zoo;  
zoo.emplace_back(new Elephant);  
zoo.emplace_back(new Zebra);  
zoo.emplace_back(new Bear);  
cout << "Feeding time (f) or Bedtime (b)?"  
char c;  
cin >> c;  
void (Animal::*ap)()  
= c == 'f' ? &Animal::eat : &Animal::sleep;  
for (auto it = zoo.begin(); it != zoo.end(); it++) {  
((**it).*ap)();  
}
```

## References ##
- like pointers but different
	- References allow one object to be shared among different variables
	- they are set on creation and never changed
	- cannot be null:
```
struct A {  
A(int &i) : j(i) {}  
A(int &i) { j = i; } // Ill-formed!  
int &j;  
};
```

### Not all callables can be assigned to a function pointer ###
- can only assign a lambda to a function pointer if it does not have a capture list
- can't assign a functor to a function pointer
```
struct WeightedMean {  
WeightedMean(vector<double> const &weights)  
: weights(weights) {}  
double operator()(vector<double> const &data) {  
return  
inner_product(data.begin(), data.end(),  
weights.begin(), 0.0)  
/ accumulate(weights.begin(), weights.end(), 0.0);  
}  
vector<double> weights;  
};  
WeightedMean wm({1.2, 3.4});  
wm(getHomePrices()); // Fine  
double (*averager)(vector<double> const &)  
= WeightedMean({1.5, 3.6, 4.2}); // Error!  
cout << "The average home price is ";  
cout << averager(getHomePrices()) << endl;
```

## `std::function` ##
- in C++, functions aren't the only thing that can be called
	- function
	- lambda
	- functor
	- member function
### `std::function` can hold anything callable ###
```
struct WeightedMean {  
WeightedMean(vector<double> const &weights)  
: weights(weights) {}  
double operator()(vector<double> const &data) {  
return  
inner_product(data.begin(), data.end(),  
weights.begin(), 0.0)  
/ accumulate(weights.begin(), weights.end(), 0.0);  
}  
vector<double> weights;  
};  
function<double(vector<double> const &)> averager  
= WeightedMean({1.5, 3.6, 4.2}); // OK  
cout << "The average home price is ";  
cout << averager(getHomePrices()) << endl;
```
- you can even put a member pointer in `std::function`:
```
struct A {  
int i;  
};  
function<int(A *)> fp = &A::i;  
A a;  
fp(&a);
```
- you can choose between `std::function` and a template
```
template<typename Callable>  
double  
tmpl_apply(Callable c, vector<double> const &data)  
{ return c(data);  
}  
double  
fn_apply(function<double(vector<double> const &)> c, vector<double> const &data)  
{ return c(data);  
}  
void f()  
{ tmpl_apply(mean, {1.7, 2.3}); // OK  
fn_apply(mean, {1.7, 2.3}); // OK  
tmpl_apply(WeightedMean({1.2, 3.4}), {1.7, 2.3}) // OK  
fn_apply(WeightedMean({1.2, 3.4}), {1.7, 2.3}) // OK  
}
```

## How does `unique_ptr` work? ##
- suppose `auto ap = make_unique<A>(1,2);`
	- how does this get created under the hood?
		- First, `make_unique` calls
			- `new A(1,2)`
		- the compiler's first step in this "new expression" is to allocate memory
			- `operator new(sizeof(A))`
		- finally, it constructs an `A(1,2)` object in the memory that was allocated


