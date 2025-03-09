# Week One: Overview ##
## C++ is (partially) a standard ##
- while there is a carefully worded standard for C++
	- many aspects of C++ are not standardized
		- this is mostly done to match the hardware they run on
			- whether `char` is signed, bits in an `int`, etc.
	- Standardized > implementation defined > undefined
	- avoid nonstandard vendor-specific extensions to C++
## C++ is a lightweight abstraction language ##
- programming languages typically fall into a tradeoff between being good for programmers (i.e. abstract) or good for computers (i.e. low memory, fast, and low-level)
- we need both as performance vs. abstraction constantly looms over almost all programming decisions
	- do I want to do it the clear, modular, maintainable way, or the performant way?
		- C++ gives you the best of both worlds
			- you can create powerful abstractions that are lightweight while still remaining performant
				- all the abstraction is "compiled away" during the build
					- no performance penalty
## Hello World ##
```
#include<iostream>  
#include<format>  
using namespace std;  
int  
main()  
{  
string name;  
cout << "What's your name? ";  
cin >> name;  
cout << format("Hello, {}!\n", name);  
return 0;  
}
```

## Defining functions ##
```
int square(int n)  
{  
return n*n;  
}  
auto square(double n)// OK to have two functions with  
{ // same name (overloading) as long  
return n*n; // as compiler can tell which you  
} // mean by context  
// The type "auto" means the compiler  
int main() { // should perform type inferencing  
cout << square(2) + square(3.1416);  
return 0;  
}
```

## Function templates ##
```
auto square(auto n)  
{  
return n*n;  
}  
int main()  
{  
return square(2) + square(3.1416);  
}
```
- prior to C++20, function templates had a different form:
```
template<typename T>  
auto square(T n)  
{  
return n*n;  
}  
int main()  
{  
return square(2) + square(3.1416);  
}
```

# Lecture 2 and 3: Objects, Classes, and Their Lifetimes #

## Copying and assignment ##
- in C++, when we do an assignment, the object is copied:
```
int i = 3;  
int j = i;  
i = 5;  
cout << j; // Prints 3
```

![[Pasted image 20250308085347.png]]
- both `i` and `j` have their own storage associated with them in the running program

## References ##
- suppose we wanted `j` to refer to the same data in memory as `i`, instead of a copy
	- we can do this by creating `j` as a reference to an existing object
		- `int &`
```
int i = 3;  
int &j = i;  
i = 5;  
cout << j; // Prints 5
```

![[Pasted image 20250308085741.png]]
## `unique_ptr` ##
- gives you access to the data in the object
	- when you need a reference to the object managed by the `unique_ptr`, use the `*` operator
```
unique_ptr<int> ui = make_unique<int>(5);  
cout << *ui; // Prints 5
```
- the `unique_ptr` manages the lifetime of the object
	- when the `unique_ptr` goes away, it will automatically free up the memory of the object that it is managing
## Updating a `unique_ptr`
- if you bind a `unique_ptr` to point to a new object, it will free up the old one before it starts to manage the new one
```
unique_ptr<int> ui = make_unique<int>(5);  
cout << *ui; // Prints 5  
ui = make_unique<int>(2);  
cout << *ui; // prints 2
```
- the `unique_ptr` automatically releases the memory of the first object (the one with value `5`) before it starts managing the new object (the one with value `2`)

## Transferring Ownership ##
- it's a little tricky to transfer ownership from one `unique_ptr` to another
	- assignment doesn't work
		- `up2 = up1; // Oops! Two "unique" owners`
	- you have to move it
		- `up2 = move(up1); //up2 is owner, don't use up1`
## A Class is how we create our own types ##
- classes have member functions (methods) and data members (fields)
```
struct Student_info { // In header  
string name;  
double midterm, final;  
vector<double> homework;  
// Method to calculate the student’s grade  
double grade() const {  
return (midterm + final + median(homework))/3;  
}  
};
```

## Accessing methods and fields ##
- use the `.` operator
```
Student_info s;  
s.name = "Mike";  
s.midterm = 70;  
s.final = 85;  
s.homework.push_back(60);  
s.homework.push_back(75);  
cout << s.grade();
```

## Static vs. Dynamic types
- a program uses expressions to refer to objects in memory
- the static type is type of the expression
	- this is known at compile time
- the dynamic type is the type of the actual object referred to by the expression
	- this may only be knowable at run-time
- static and dynamic type generally only differ due to inheritance

## Single inheritance ##
- one can use inheritance to model an "isA" relationship
	- `struct Animal { /* ... */ };`
	- `class Gorilla: public Animal {...};`
	- This means that a Gorilla "isA" `Animal` and can be referred to by `Animal` references

## Static type vs. Dynamic type ##
```
int i = 5; // S = int, D = int  
Gorilla g; // S = Gorilla, D = Gorilla  
Animal &a = g; // S = An&, D = Gor  
Animal a2 = g; // Oops! Can’t copy a Gorilla  
// into an Animal  
unique_ptr<Animal> ua = make_unique<Gorilla>();  
// Static type of *ua is Animal but Dynamic is Gorilla  
ua = make_unique<Falcon>();  
// Now S = Animal, D = Falcon
```

## Virtual vs. non-virtual method ##
- a virtual method uses the dynamic type
- a non-virtual method uses the static type
```
struct Animal {  
void f() { cout << "animal f"; }  
virtual void g() { cout << "animal g"; }  
};  
struct Gorilla : public Animal{  
void f() { cout << "gorilla f"; }  
void g() { cout << "gorilla g"; }  
void h() { cout << "gorilla h"; }  
};  
void fun() {  
unique_ptr<Gorilla> g = make_unique<Gorilla>;  
Animal &a = *g;  
a.f(); // Not virtual: Animal’s f  
a.g(); // Virtual: Gorilla’s g  
a.h(); // Error: h is not in animal  
(*g).f(); (*g).g(); (*g).h(); // Gorilla’s f, g, and h  
}
```

## Passing arguments ##
- there are 3 ways to pass arguments to a function:
	- by value:
		- `void f(X x);`
			- `x` is a copy of the caller's object
			- Any changes `f` makes to `x` are not seen by the caller
	- by reference:
		- `void g(X &x);`
			- `x` refers to the caller's existing object
				- does not create a new copy
			- any changes `g` makes to `x` will affect the data in memory
				- will change what the caller sees
	- by move:
		- `void h(X &&x);`

## Invoking a constructor to create an object ##
```
// construction.cpp on chalk  
#include<initializer_list>  
using std::initializer_list  
struct A {  
A(int i, int j = 0); // #1  
explicit A(int i); // #2  
};  
struct B {  
B(int i, int j); // #3  
B(initializer_list<int> l); // #4  
};  
int main() {  
A a0 = 3; // A(3, 0) #1  
A a1(3); // A(3) #2  
A a1{3}; // A(3) #2. Uniform init: best practice but  
B b0(3, 5); // #3  
B b00 = {1, 2, 3, 4}; // #4  
B b1 = { 3, 5}; // #4  
B b2{3, 5}; // #4 Initializer list is preferred  
}
```

## Default constructor ##
- if you don't declare any constructors, C++ will create a no-argument constructor for you
- consider `struct S {int i; double d;}`
	- the compiler will generate a default constructor for you like so:
```
struct S {  
S() {}  
int i;  
double d;  
};
```

## Copy constructors ##
- the compiler implicitly generates a copy constructor that copies all of the base classes and members as if you wrote this:
```
struct S {  
S(S const &s) : i(s.i), d(s.d) {}  
int i{}; double d = 1.2;  
};
```

## Move constructors ##
- if you don't define your own default, copy, or move constructor, the compiler generates a move constructor that moves all the members and base classes:
```
struct S {  
S(S &&s) : i(move(s.i)), d(move(s.d)) {}  
int i{}; double d = 1.2;  
};
```

## Copy and move assignment ##
- the compiler generates similar copy and move assignment (`operator=()`) operators as well as constructors
	- you can explicitly define any of these constructors, destructors, or assignment operators if you don't want the default behavior

## Type conversions ##

### Implicit Conversions ###
- built in
```
int i = 780;  
long l = i;  
char c = 7;  
char c = i; // No warning, but dangerous!
```
- polymorphism
```
unique_ptr<Animal> ap = make_unique<Dog>("Champ");
void f(Dog &dr) { Animal &ar = dr; }
```
- user-defined
	- constructors
	- operator overloading
- "Standard conversions"
	- defined in clause 4 of the standard

### Constructors and typecasts ###
```
struct A {  
A();  
A(int i); // Creates a type conversion  
A(int i, string s);  
explicit A(double d); // Does not create a conversion  
};  
A a;  
A a0(1, "foo");  
A aa = { 1, "foo"};  
A a1(7); // Calls A(int)  
a1 = 77;// ok  
A a3(5.4); // Calls A(double)  
a3 = 5.5; // Calls A(int)!!
```

### Type conversion operators ###
```
struct seven {  
operator int() { return 7; }  
};  
struct A { A(int); }  
int i = seven();  
A a = 7;  
A a = seven(); // Ill-formed, two user-defined  
// conversions will not be implicitly composed
```

### Explicit conversions ###
- old-style C casts
	- `char *cp f(void *vp) {return (char *)vp; }`
		- Legal but bad!
- new template casting operators
	- `static_cast<T>`
		- like C casts, but only makes conversions that are always valid
			- i.e. convert one integral type to another
				- truncation may still occur
	- `dynamic_cast<T&>`
		- casts between reference types
			- can cast between a `Base&` and a `Derived&` but only does the cast if the target object really is a `Derived&`
		- only works when the base class has a vtable
			- the compiler adds a secret virtual function that keeps  track of the real run-time type of the object
		- if the object is not really a `T &` dynamic, `dynamic_cast<T>` throws `std::bad_cast;`
	- `reinterpret_cast<T*>`
		- does a bitwise reinterpretation between any two pointer types
			- even for unrelated types
		- never changes the raw address stored in the pointer
		- can convert between integral and pointer types
	- `const_cast<T>`
		- can change constness or volatileness only
		- usually better alternatives
## The C++ Object Lifecycle ##
### Object duration ###
- Automatic storage duration
	- local variables
	- lifetime is the same as the lifetime of the function/method
- Static storage duration
	- global and static variables
	- lifetime is the lifetime of the program
- Dynamic storage duration
	- lifetime is explicit
	- created with commands like `make_unique`
	- programmer controls when it ends
- with all durations, the constructor is called when the object is created and the destructor is called when the object is destroyed

# Lecture Four

## Lambdas ##
- besides named functions, you can also call lambdas
	- ` [](int x)->int { return x*x; }`
- if your lambda does not contain return statements that return different types, the compiler can usually figure out the return type without being specified
```
[](int x, int y) {
int z = x+y; return z+x; // the compiler is smart enough to return an int
}
```
- if needed, the return type can be explicitly set
```
[](int x, int y) -> int {
int z = x+y; return z+x;
}
```

## Capture Lists ##
- to capture local variables by references, use `[&]`
- to capture local variables by value, use `[=]`
```
auto f() {  
int i{3};  
auto lambda = [=](){ cout << i; }; // lambda has a copy of i  
return lambda;  
}  
// Even if the original i has gone away, you can still run lambda  
f()(); // prints 3 even though original i no longer exists
```
- C++ makes `const` copies, so they may be hard to modify
	- can be disabled with the `mutable` keyword:
```
int i{3};  
auto lambda = [=]() mutable { cout << i++; }; // lambda has a copy of i  
// Note: Only modifies lambda’s i, not the original variable
```

## Functors ##
- if you write your own class with an overloaded `operator()`, it is called a functor
- functors are stateful objects that can be called like functions:
```
struct Accumulator {  
int operator()(int j) { i = i + j; return i }  
int i = 0;  
};  
Accumulator acc;  
cout << acc(1); // Prints 1  
cout << acc(2); // Prints 3 = 1+2  
cout << acc(4); // Prints 7 = 1+2+4
```
- lambdas are often implemented as functors

## Move semantics ##
### What does it mean to move an object? ###
- moving `y` to `x`
	- `x = move(y)`
		- this means the value of `x` is the same as what `y` started at
	- one way to do this is by using ordinary copying
		- `x = y`
	- the difference between a copy and a move is that `y` is allowed to be changed by the move
		- subsequent code should not use the value of `y`
### Why move rather than copy? Efficiency ###
- since a move can "raid the old object for parts", it can be a lot more efficient
	- copying a tree requires copying all the branches
	- moving a tree only requires moving the root
![[Pasted image 20250308094258.png]]
### How does C++ know whether to move or copy? ###
- if it's sure the old object doesn't need its value any more
	- if the old object is a temporary value and has no name
		- thus it can't be referred to again
		- it's safe to move `unique_ptr`s for this reason
			- `auto x = make_unique<X>(); //x owns the X object`
	- the other way is if the programmer uses `std::move` to say it's okay to move
		- `auto y = move(x); // now y owns the X object`
### How does move work? Rvalue references ###
- a reference with `&&` instead of just `&` can bind to a temporary and move it elsewhere
- objects are often much cheaper to copy than move
	- deep copying a tree
	- some objects like `unique_ptr` can be moved but not copied
```
template<class T>  
void swap(T& a, T& b)// "perfect swap"(almost)  
{  
T tmp = move(a); // could invalidate a  
a = move(b); // could invalidate b  
b = move(tmp); // could invalidate tmp  
}
```

### How do I make a type movable? ###
- you don't *need* to make a class movable
	- it's just a performance operation because C++ will just copy if there are no move operations
	- if it's more efficient, you should create move constructors and move assignment operators:
```
template<class T> class vector {  
// ...  
vector(vector<T> const &); // copy constructor  
vector(vector<T> &&); // move constructor  
vector& operator=(const vector<T>&); // copy assignment  
vector& operator=(vector<T>&&); // move assignment };
```
- sometimes the compiler will automatically generate move constructors and move assignment operators that just move all the members
	- if you want to force the compiler to generate the default move constructor even though it wouldn't normally, you can do that with `default`
```
struct S {  
S(S const &); // OK, but stops move constructor generation  
S(S &&) = default; // Gets it back  
/* ... */  
};
```

### Rule of five? ###
- a lot of discussion about whether the rule of 3 should be extended to a "rule of 5"
	- if you define any of:
		- the destructor
		- the copy constructor
		- copy assignment operator
		- move constructor
		- move assignment operator
	- you should review that they all do the right thing
		- they're all related

## Templates ##
### OO and Templates can solve similar problems ###
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
- does this code need OO?
	- not really...
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

### Concepts
- concepts play the analogous role for generic programming that base classes do in object oriented programming
	- a concept explains what operations a type supports
- this encapsulates the same info as the base class:
```
template<typename T>  
concept Animal = requires(T a) {  
{ a.eats() } -> convertible_to<string>;  
{ a.name() } -> convertible_to<string>;  
};
```

### Now, we can ensure that `a` represents an animal ###
- with the above concept, we can specify that `a` must satisfy the `Animal` concept
	- the compiler will not let us initialize it with a non-`Animal` type
```
int main() {  
Animal auto a = Cat();  
cout << "A " << a.name() << " eats " << a.eats();  
}
```

### Specializing and overloading templates ###
 - the "secret sauce" for C++ templates is that if the general "generic" definition of the template isn't really what you want, you can override it for that particular case with a specialization
	 - compile-time analog to object orientation where you also override a more general method in a more specialized derived class
### Matrix determinants ###
- the determinant is a number that represents "how much a matrix transformation expands its input"

### Overloading ###
- function templates can be overloaded

### Full specialization ###
- a function, class, or member can be fully specialized
	- i.e. `Matrix<1,1>::determinant()`
### Partial specialization ###
- only classes may be partially specialized
- consider this template class:
```
template<class T, class U>  
class Foo { ... };
```
- partial specialization of that class:
```
template<class T>  
class Foo<T, int> {...};
```
- the `<>` after the class name indicates it's a specialization
- the partially specialized class has no particular relation to the general template class
	- you either need to redefine or inherit common functionality
		- inherit preferred
## `static_assert` ##
- C++ lets you create a compile-time assertion that prints an error message
	- `static_assert(rows == cols, "Sorry, only square matrices have determinants.");

## `*this` ##
- sometimes a method needs a reference to the object it's a part of
	- `*this`
```
template<class T, int h, int w>  
T  
Matrix<T, h, w>::determinant() const  
{  
return determinantImpl(*this);  
}
```

## `constexpr` ##
### `constexpr` allows you to do things at compile-time ###
- programming is normally a way to write code that runs at runtime
	- is surprisingly common that you need things to take place at compile-time instead
### `constexpr` functions ###
- a `constexpr` function cannot contain just any code
	- some things that are not allowed:
		- uininitialized declarations
		- `static` or `thread_local` declarations
		- modification of objects that were not created in the function
			- potential modifications by calling a non-constexpr function
		- virtual methods
		- non-literal return types or parameters
			- a type is literal if its constructor is trivial or `constexpr`
```
auto square(int x) constexpr  
{ return x*x; }  
Matrix<square(3), square(3)> m; //this works because of constexpr
```

## tuples ##
- a tuple is an anonymous struct with fields of given types
	- can think of them as having the same relation to structs as lambdas do to functions
```
tuple<string, int, double> si("str", 2, 3.5);  
tuple di(2.5, 3, 'c');  
// CTAD: di will be a tuple<double,int, char>  
cout << get<0>(di) // prints 2.5  
cout << get<char>(di); // prints 'c'  
int three = get<1>(di);
```

## Returning multiple values
- one natural use for pair and tuple is to let functions return multiple values
	- Python just lets you do it
```
pair<int, int> f() {  
return {1, 2}; // ok  
}
```

```
tuple<int, int, char> g() {  
return {1, 2, 'u'}; // OK starting in C++17  
}
```

## Structured bindings ##
- suppose we have a function that either returns a web page or a network error code
```
pair<unique_ptr<Page>, int> getWebPage() {  
/* ... */  
if(succeeded)  
return { page, 0 };  
else  
return { unique_ptr<Page>, errCode };  
}  
/* ... */  
auto [ page, err] = getWebPage();  
if(!err) processPage(page)  
else if (err == 404) { cout << "Not found" << endl; }  
else { cout << format("Unknown error {}\n“, err); }
```
- how can we pass our arguments as-is to another function using that function's signature?
	- this is what `std::forward` is designed for
```
// Perfectly forwards everything  
template<typename T, typename... Args>  
unique_ptr<T> makeUnique(Args &&... args)  
{  
return unique_ptr<T>(new T(forward<Args>(args)...));  
}
```

# Threads #

- Hello threads:
```
#include <iostream>  
#include <thread>  
void hello_threads() {  
std::cout<<"Hello Concurrent World\n";  
}  
int  
main(){  
// Print in a different thread  
std::thread t(hello_threads);  
t.join(); // Wait for that thread to complete  
}
```

## Locks ##
```
std::list<int> some_list; // A data structure accessed by multiple threads  
std::mutex some_mutex; // This lock prevents concurrent access to the shared data structure  
void  
add_to_list(int new_value)  
{  
// Since I am going to access the shared data struct, acquire the lock  
std::lock_guard guard(some_mutex); // CTAD deduces lock_guard<mutex>  
some_list.push_back(new_value);  
// Now it is safe to use some_list. lock_guard destructor releases lock at end of function  
/* ... */  
}  
bool  
list_contains(int value_to_find)  
{  
std::lock_guard guard(some_mutex); // Must lock to access some_list  
return std::find(some_list.begin(),some_list.end(),value_to_find) != some_list.end();  
}
```

## Reader-Writer Locks ##
- `shared_mutex`
```
std::list<int> some_list;  
std::shared_mutex some_mutex;  
void  
add_to_list(int new_value)  
{  
std::unique_lock guard(some_mutex); // Unique writer access  
some_list.push_back(new_value);  
}  
bool  
list_contains(int value_to_find)  
{  
std::shared_lock guard(some_mutex); // Shared reader access  
return  
std::find(some_list.begin(),some_list.end(),value_to_find) != some_list.end();  
}
```

## Lock Ordering ##
- if you want to avoid deadlocks, you want to acquire locks in the same order
	- suppose thread 1 acquires lock A and then lock B
	- and thread 2 acquires lock B and then lock A
		- there is a window where thread 1 owns lock A and wants to acquire lock B, and thread 2 owns lock B and wants to acquire lock A
			- both of them will be waiting forever
	- best practice is to document an order of locks and always acquire them in that order
		- tools like ThreadSanitizer can also find potential deadlocks and data races
## It's sometimes hard to fix a lock order ##
```
class account {  
mutex m;  
currency_value balance;  
public:  
friend void transfer(account& from,account& to,  
currency_value amount) {  
lock_guard lock_from(from.m);  
lock_guard lock_to(to.m);  
from.balance -= amount;  
to.balance += amount;  
}  
};
```
- if one thread transfers from account A to account B, while another thread transfers from account B to account A, this will cause a deadlock
	- no easy solution under this paradigm while maintaining the multithreading property of the program
		- but there is a solution...
## `std::scoped_lock` ##
- `std::scoped_lock` allows you to acquire multiple locks "at the same time" and ensure there will be no deadlock
	- this works by release and acquiring locks in different orders until it works
```
class account {  
mutex m;  
currency_value balance;  
public:  
friend void transfer(account& from,account& to,  
currency_value amount) {  
scoped_lock lck(from.m, to.m);  
from.balance -= amount;  
to.balance += amount;  
}  
};
```

# Exceptions and RAII #
## Exceptions ##
- when something goes wrong, throw an exception
	- ensures errors are never inadvertently ignored
- you can catch an exception within a `try` block with `catch`
	- if you don't, it's passed to the caller, and the caller's caller and so on until it is caught
## RAII ##
- "Resource Acquisition is Intialization"
- uses automatic duration objects to manage the lifetimes of dynamic duration resources
- just means we ensure that an automatic object's destructor performs any needed cleanup

## RAII Classes ##
- all of these classes use their destructor to release the object they manage:
	- `unique_ptr`
	- `shared_ptr`
	- `lock_guard`
	- `unique_lock`
	- `shared_lock`
	- `jthread`
## What if we didn't use RAII?
```
std::list<int> some_list;  
std::shared_mutex some_mutex;  
void  
add_to_list(int new_value)  
{  
some_mutex.lock();  
some_list.push_back(new_value);  
some_mutex.unlock();  
}  
bool  
list_contains(int value_to_find)  
{  
some_mutex.shared_lock();  
auto result =  
std::find(some_list.begin(),some_list.end(),value_to_find) != some_list.end();  
some_mutex.shared_unlock();  
return result;  
}
```

## Cache-conscious programming ##
- objects or fields that are accessed independently should be aligned and padded so they end up on different cache lines
- keep read only data separate from data that is modified frequently
- when possible, split an object into thread-local pieces
	- a counter used for statistics could be split into an array of counters, one per thread, each on a separate cache line
		- a shared counter would cause invalidation traffic, but the split counter lets each thread update its own replica
			- can simply sum up all the counters at the end
- if a lock protects data that is frequently modified, then keep the lock and the data on distinct cache lines
	- threads trying to acquire the lock will thus not interfere with the lock-holder's access to the data
- if a lock protects data that is frequently uncontended, try to keep the lock and the data on the same cache lines
	- thus, acquiring the lock will also load some data into the cache
- if a class or struct contains a large chunk of data whose size is divisible by a high power of two, consider separating it out of the class
	- hold it with a `unique_ptr`
- use a profiling tool like VTune to identify cache bottlenecks

# Systems Programming #
## Pointers ##
- pointers point to the address of an object of the given type
	- `A *ap = new A();`
		- `ap` contains the address of an object of type `A` in memory
		- `new A()` constructs an object of type A in memory and returns its address
		- `new A()` has the same relation to `A *` as `make_unique<A>()` has to `unique_ptr<A>`
```
A a;
ap = &a; // & is the addressof operator
```

- dereference a pointer with `*`
	- `A a = *ap;`
- `->` is an abbreviation for `(*_)`
	- `ap->foo(); //same as (*ap).foo()`
- all of these should be familiar from `unique_ptr`
- WARNING: raw pointers do not have destructors and do not manage the lifetime of the object they point to
	- simply store its address
	- memory leak risk
# Inter-Thread Communication #
## Sometimes locks aren't what you want ##
- suppose you want a "producer/consumer" design pattern
	- like a supply chain
	- some threads produce work items
		- other threads consume those items
			- incredibly common paradigm
	- the producers put work into a queue, and the consumers take them off
- locks can allow thread-safe access to the queue
	- but what if there is no work?
		- the consumer thread needs to go to sleep and then wake up once there's work to do
		- you don't want a lock, you'd like to wait for an "event" stating the queue has become non-empty
## Producer-consumer implementation ##
- there are couple of new library features
	- `unique_lock`
		- richer version of `lock_guard`
	- `condition_variable`
		- "wakes up" the waiting threads
## Condition variables ##
- the C++ version of an event
	- `condition_variable cv;`
- you can wait for a condition variable to signal
```
mutex m;  
boolean test();  
unique_lock<mutex> lck(m);  
cv.wait(lck, test);
```
- if the test succeeds, the wait returns immediately, otherwise it unlocks `m`
	- this is why we use a `unique_lock` instead of `lock_guard`
	- once the `condition_variable` signals the waiting thread...
		- the lock is reacquired
		- the test is rerun
			- if it fails, we wait again
				- protects against spurious wakeups
		- if the test succeeds, the program continues
## Signaling an event is simple ##
- `cv.notify_one();`
	- wakes one waiter
		- no guarantees which one
- `cv.notify_all();`
	- wakes all waiters
## Producer-Consumer implementation ##
```
mutex m;  
queue<data_chunk> data_queue;  
condition_variable cond;  
void data_preparation_thread()  
{ while(more_data_to_prepare()) {  
data_chunk const data=prepare_data();  
lock_guard lk(m);  
data_queue.push(data);  
cond.notify_one();  
}  
}  
void data_processing_thread()  
{ while(true) {  
unique_lock<mutex> lk(m);  
cond.wait(lk,[]{return !data_queue.empty();});  
data_chunk data=move(data_queue.front());  
data_queue.pop();  
lk.unlock();  
process(data);  
if(is_last_chunk(data))  
break;  
}  
}
```


## Async functions: Running functions in another thread ##
- it's nice that we can pass arguments to thread like we do functions, but how do we get values back?
	- we want to use threads as "asynchronous functions"
		- `std::future<int> f = std::async(func_returning_int);`
- C++11 defined `std::future`, a class template that lets a thread return a value sometime in the future when its calculation is complete
- one way to create a future is with `std::async`
	- as soon as you create it, it starts running the function you passed it in a new thread
	- call `get()` when you want to get the value produced by the function
	- `get()` will wait for the thread function to finish, then return the value
## Introducing promises and futures ##
- promises and futures let you pass values between threads
	- kind of like producer/consumer but you're just passing a single item
		- no need for a queue
	- a promise lets you pass a value from one thread to another
		- create a paired future and promise:
```
promise<int> p;
future<int> fi = p.get_future();
```
- the producing thread stores the value in the promise:
	- `p.set_value(7);`
- the consuming thread receives the value when it becomes available
	- `cout << "received " << fi.get() << endl;`
```
#include <future>  
#include <iostream>  
int calculate_the_answer_to_LtUaE();  
void do_stuff();  
int main()  
{ // Run calculation in a different thread  
std::future<int> the_answer  
= std::async(calculate_the_answer_to_LtUaE);  
do_stuff();  
std::cout <<"The answer to life, the universe and everything is "  
<< the_answer.get()  
<< std::endl;  
}
```

# OO Techniques #
## tuples ##
- a tuple is an anonymous struct with fields of given types
	- a tuple has a similar relation to structs as lambdas do to functions
	- use `get<>` to access the fields
```
tuple<string, int, double> si("str", 2, 3.5);  
tuple di(2.5, 3, 'c');  
// CTAD: di will be a tuple<double,int, char>  
cout << get<0>(di) // prints 2.5  
cout << get<char>(di); // prints 'c'  
int three = get<1>(di);
```

## Structured binding ##
- suppose we have a function that returns either a web page or a network error code
```
pair<unique_ptr<Page>, int> getWebPage() {  
/* ... */  
if(succeeded)  
return { page, 0 };  
else  
return { unique_ptr<Page>, errCode };  
}  
/* ... */  
auto [ page, err] = getWebPage();  
if(!err) processPage(page)  
else if (err == 404) { cout << "Not found" << endl; }  
else { cout << format("Unknown error {}\n“, err); }
```

## Variants: Basic use ##
- a variant is like a tuple, but only contains one of its fields at a time
```
variant<int, double> v = 3; // Holds int  
get<0>(v); // Returns 3  
get<1>(v); // Throws std::bad_variant_access  
v = 3.5; // Now holds a double  
get<double>(v); // Returns 3.5
```
- you're also able to check what's in your variant:
```
v.index; // returns 1  
holds_alternative<double>(v); // returns true  
holds_alternative<int>(v); // returns false
```

## SOLID Principles ##
- Single Responsibility Principle
- Open/Closed Principle
- Liskov Substitution Principle
- Interface Segregation Principle
- Dependency Inversion Principle

## Create and extend classes with OO ##
- full runtime dispatch
- must inherit
	- very safe "nominal" typing
	- can result in hard to maintain "spaghetti inheritance"
- objects are expensive to create with `make_unique`

## Create and extend classes with templates ##
- compile-time dispatch
- can work with values instead of `unique_ptr`s
	- efficient
	- great open/closed support
	- not possible to use when runtime dispatch is needed
	- or for containers that need fixed types
		- `using Zoo = vector<???>;`
## Create and extend classes with duck-typed variants ##
- supports runtime dispatch
- can work with values instead of `unique_ptr`s
- efficient
- notation can be clunky

## Type erasure ##
- can be thought of as classes that act like templates
- eliminates the "viralness" of templates
	- lets you get back to honest-to-goodness classes
- used in the standard library by `std::function` and `std::any`
- with types erased, "Open-Closed" extension is difficult

## Static Polymorphism ##
- used to be very common
	- people wanted to produce high performance code that "looks like" inheritance and overriding "OO"
		- "traditional OO is better"
- is now less common
	- lots of boilerplate
	- combines the disadvantages of templates...
		- no runtime dispatch
	- with the disadvantages of OO
		- inheritance can be a headache

