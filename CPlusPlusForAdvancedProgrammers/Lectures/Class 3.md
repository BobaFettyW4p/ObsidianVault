# What Can Go In a Class? #
- previously seen:
	- ordinary data members (fields)
	- member functions (methods)
- these are not the only items a class can contain
	- Static Class Members
		- usually a class member requires you to know what object it applies to
```
struct S { int i; double d };  
S s; // Need an s to  
s.i = 3; // access i
```

 - Sometimes you want the same class member to be shared by all objects of a class
```
struct CountedObject {  
CountedObject() { objectCount++; }  
~CountedObject() { objectCount--; }  
static size_t objectCount;  
};  
CountedObject c1, c2;  
cout << CountedObject::objectCount; // 2
```

- Initializing static class members
	- you may have to initialize static data members with class types in a separate .cpp file
		- this ensures its memory is allocated once, not every time the header is included
## Methods are also able to be static ##
```
struct CountedObject {  
CountedObject() { objectCount++; }  
~CountedObject() { objectCount--; }  
static size_t memUsed() {  
return objectCount * sizeof(CountedObject);  
}  
static size_t objectCount;  
};  
CountedObject c1, c2;  
cout << format("{} objects using {} chars",  
CountedObject::objectCount,  
CountedObject::memUsed());
```

# Constructors and Destructors #
- a Constructor is invoked to create an object
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
- "Constructors" for fundamental types
	- since they don't' have them by default, creating one without an initializer gives you garbage values
		- `int i; //i could be initialized to any value`
		- reading uninitialized memory is a common source of bugs
		- if you explicitly pass it zerp constructor arguments, the memory is zero-initialized
			- `int i(); // i is 0`
- special constructors
	- some make it easier to create objects
		- C++ will automatically create them
			- default constructor
			- copy constructor
			- aggregate constructor
			- move constructor - will be covered later
- Default Constructors:
	- if you don't declare any constructors, is it impossible to construct an object of the type?
		- i.e. `struct S { int i; double d; };`
		- No, it breaks compatibility with C
	- if you don't declare a constructor, C++ will create a no-argument constructor for you
		- if you declare a `struct` S as:
			- `struct S { int i; double d; };`
		- the compiler will generate it as:
```
struct S {
S() {}
int i;
double d;
};
```

-  this default constructor may not be optimal for your needs (uninitialized memory)
	- you can always write your own default constructor instead
		- in this case, the compiler will not generate one for you
```
struct S {
S(): i(), d(1,2) {}
int i; int j;
};
S s; // s.i==0, s.j==1.2
```

## Non-Static Data Member Initializers (NSDMI) ##
- you can provide default initializers for members and the default constructor will construct with those values
```
struct S {
int i=0; double j=1.2; //very similar to Python
};
S s; //s.i==0, s.j==1.2
```

- for technical reasons, static data members cannot be initialized in class
## What if you don't want a default constructor at all? ##
- if you define any constructor, the compiler won't generate a default constructor

## Copy Constructors ##
- besides default construction, you are also able to construct a new object by copying an existing one
```
S s;
S s2 = s;

// is this legal?
// it has to be, it's valid C
// what constructor does it call?
```

- the compiler implicitly generates a copy constructor that copies all of the base classes and members
	- as if you wrote:
```
struct S {
S(S const &s) : i(s.i), d(s.d) {}
int i{}, double d = 1.2;
};
```

## What if the compiler generates the wrong copy constructor? ##
- while convenient, the default copy constructor is not always correct
- in a binary tree class, the default copy constructor will only copy the root
	- this leaves us with tangled tree
		- i.e. two separate root nodes for 2 separate binary tree objects that point to same nodes in the same tree hierarchy
			- if you delete an object from tree 1, it is also deleted for tree 2 and so on
		- you have to write your own copy constructor in these cases
```
struct BinaryTree {  
BinaryTree(BinaryTree const &b) { /* ... */ }  
/* ... */  
};
```

## What if I don't want a copy constructor at all? ##
- an important design pattern is the "Singleton Pattern"
	- represents a class that only ever has one object
		- thus, you would not want a copy constructor at all
		- need to delete the copy constructor like so
```
struct Logger {  
Logger(Logger const &) = delete;  
static Logger myLogger;  
private:  
Logger() { /* ... */; }  
/* ... */  
};
```

## Aggregate construction ##
- what if we wanted to create `S` with specific values for `i` and `d`?
	- `S s= {5,6.7}`
	- this is valid C (and thus legal)
	- what constructor does this call?
- if a class ia simple data structure with public members, C++ considers it to be an aggregate class and generates a constructor that takes an initializer for every field
	- thus, S behaves as if it had the following constructor:
```
struct S { S(int const &i, double const &d) : i(i), d(d) {} ... };  
S s = {1, 2.3}; // OK  
S s2 = {.i =1, .d = 5.2 }; // OK. Designated initializer
```

## Destructors ##
- classes have Destructor methods that do any needed cleanup when the object goes away
- when an object in memory goes away, its destructor is always called
- you can write a destructor for a class:
```
struct A {
~A() {
/*Code to cleanup resources used by A*/
}
/* ... */*
};
```

if you don't write one, the compiler will generate one for you
- the default destructor
	- simply destroys all fields and base classes in the reverse of the order they were created

# `unique_ptr` #
- a class that manages an object in memory
	-  it can give you a reference to the object
	- the object it manages is always cleaned up when the `unique_ptr` goes away

```
void f() {  
// Create a unique_ptr with make_unique  
auto up = make_unique<vector<int>>({1, 5, 3});  
sort(up->begin(), up->end());  
// up cleans up the vector when you leave f  
}
```
- `unique_ptr` has a specially written destructor that destroys the object and releases the memory
	-  memory management is ~40% of C development time, but much less in C++
- there is a special name for this - RAII

# Sorting algorithms #
- implementers use different algorithms for `sort` and `partial_sort`
	- since sorting half the range is pretty close to sorting all of the range, the most efficient sorting algorithm overall is generally going to be best
		- `partial_sort` is really designed for, say, getting the top 10 out of a million elements
- Always benchmark, don't just assume
	- even expert software developers are bad at predicting the performance of code
		- measure, measure, measure

# Passing arguments to functions and methods #
- can pass by reference:
`istream& Student_info::read(istream&);`
- as well as by value:
`istream Student_info::read(istream);`
# Objects in code and data #
- in program code, objects are used through expressions and variables
- when the program is run, the actual data objects exist in data memory
- How are C++ expressions and variables related to the actual objects in memory?
	- do they have the same type?
	- do they have the same lifetime?
	- where is the storage associated with an expression?
# Associating storage and variables #
- if an expression or variable has a value type (not a reference type), it gets its own storage
	- memory is allocated for it and a constructor is run
	- when the variable or expression leaves scope, it's destructor is run and the memory is released
- if the expression is an lvalue reference type, it's storage needs to be supplied as part of initialization
	- no memory management, construction or destruction occurs during initialization or upon leaving scope

![[Pasted image 20250305210509.png]]

## Passing arguments ##
- 3 ways to pass arguments to a function in C++:
	- By value:
		- `void f(X x);`
		- `x` is a copy of the caller's object
		- any changes `f` makes to `x` are not seen by the caller
	- By reference:
		- `void  g(X &x);`
		- `x` refers to the caller's existing object without creating a new copy
		- any changes `g` makes to `x` also change what the caller sees
	- By move:
		- `void h(X &&x);`
		- will be covered in bigger depth soon

```
void f(int x)  
{ x = 3; cout << "f’s x is " << x << endl; }  
void g(int &x)  
{ x = 4; cout << "g’s x is " << x << endl; }  
int main() {  
int x = 2;  
cout << x << endl; // prints 2  
f(x); // print’s f’s x is 3  
cout << x << endl; // prints 2  
g(x); // print’s g’s x is 4  
cout << x << endl; // prints 4  
return 0;  
}
```

## Why does only C++ make you specify how an object is passed? ##
- most languages only have one way of passign arguments
- in C, always by value
	- the function gets its own copy
- in Java, arguments are always passed by reference
- in Python, arguments are passed by "object-reference"

### Passing by value ###
- Pros
	- very safe
		- the function you call can't actually see the object it is passed
- Cons
	- may be very expensive
		- imagine copy a map with a million key-value pairs
	- doesn't work with inheritance/OO
		- if you copy an `Animal` but it's really a `Gorilla`, your copy only has `Animal` fields (virtual methods may try to reference non-existent `Gorilla` fields)
	- Sometimes you want to modify the caller's object

### Passing by reference ###
- Pros
	- more efficient
		- objects don't need to be copied
	- works with inheritance/OO
	- can modify the object if desired/appropriate

- Cons
	- dangerous to call a function by reference as you may unexpectedly modify the object
		- can be compensated for by utilizing `const`
			- `void f(int const &i) { i = 3; // illegal: i is const}`
	- managing memory is difficult

![[Pasted image 20250305211321.png]]

## some other special members ##
- type conversions
- overloaded operators
- move constructors

## Implicit Conversions ##
- built in
	- `int i = 780;`
	- `long l = i;`
	- `char c = 7;`
	- `char c = i; //no warning, but dangerous`
- polymorphism
	- `unique_ptr<Animal> ap = make_unique<Dog>("Champ");`
	- `void (Dog &dr) { Animal &ar = dr; }`
- user-defined
	- constructors
	- operator overloading
- "Standard conversions"
	- defined in clause 4 of the standard

# Constructors and typecasts #
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

## Type conversion operators ##
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

## Explicit conversions ##
- Old-style C casts
	- legal but bad
	- `char *cp f(void *vp) { return (char *)vp; }`
- new template casting operators
	- `static_cast<T>`
		- like C casts, but will only make valid conversions
	- `dynamic_cast<T&>`
		- casts between reference types
		- only works when the base class has a vtable (the compiler adds a secret virtual functions that tracks the real runtime)
		- if the object is not really a `T &`, returns `nullptr`
	- `reinterpret_cast<T*>`
		- does a bitwise reinterpretation between any two pointer types
		- never changes raw address stored in the pointer
		- can convert between integral and pointer types
	- `const_cast<T>`
		- can change const-ness or volatileness
		- usually better alternatives

## Operator overloading ##
- operators can be overloaded just like functions
	- unary operators
		- `+ - * & ~ ! ++ -- -> ->*`
	- binary operators
```
+ - * / % ^ & | << >>  
+= -= *= /= %= ^= &= |= <<= >>=  
< <= > >= == != && ||  
, [] () <=>  
new new[] delete delete[]
```

### Which way of overloading addition is better? ###
- Consider `"Hello " + myString("World")`
	- doesn't work for the member function
		- first object isn't even a class
	- `myString("World") + "Hello "`
		- this works for both
	- a global function makes sure both arguments are treated the same way
		- makes sense since addition operations are generally commutative
		- should apply the same rules to each arguments
- can also work for printing:
```
ostream &  
operator<<(ostream &os,  
myString const &ms) {...}  
myString ms("foo");  
cout << ms;
```

## How does an I/O operator get invoked? ##
- `endl` is defined as follows:
```
ostream &  
endl(ostream &os)  
{  
os << '\n';  
os.flush();  
return os;  
}
```

- why does `cout << endl;` actually behave as `endl(cout)`?
	- Another overload:
```
ostream &  
operator<<  
(ostream&os,  
ostream&(*manip)(ostream &))  
{  
return manip(os);  
}
```

- How does a `unique_ptr` work?
	- by overloading `operator->()` and `operator*()`
		- `operator->()` operates by doing `->` until it is illegal

# The C++ Object Lifestyle #
- object duration
	- automatic storage duration
		- local variables
		- object lifetime is the same as the lifetime of the function/method
	- static storage duration
		- global and static variables
		- lifetime is the lifetime of the program
	- dynamic storage duration
		- lifetime is explicit
		- create with commands like `make_unique`
		- programmer controls when it ends
	- in all cases, the constructor is called when the object is created, and the destructor is called when the object is destroyed

- automatic objects are destroyed at the end of their scope
```
struct A {  
A() { cout << "A() "; }  
~A() { cout << "~A()"; }  
};  
struct B {  
B() { cout << "B()"; }  
~B() { cout << "~B()"; }  
};  
void f() {  
A a;  
B b;  
}  
int main() { f(); return 0; }  
// Prints A() B() ~B() ~A()
```

- members have automatic duration too
```
struct A {  
A() { cout << " A() "; }  
~A() { cout << " ~A()"; }  
};  
struct C {  
C() { cout << " C()"; }  
~C() { cout << " ~C()"; }  
A a; // Goes away when C does  
};  
void f()  
{ C c;  
} // Prints A() C() ~C() ~A()
```

- static duration objects have the same lifetime as the program
	- are created (and their constructors called) when the program starts
		- in the order they are declared
		- even before `main()`
	- are destroyed (and their destructors called) when the program ends
		- right after `main()` returns

Creating static storage duration:
```
int i; // Created at program start  
struct A {  
static int j; // Created at program start  
void f() {  
static int k{}; // Created first time f() is called.  
// Does not lose its value between calls  
}  
};  
A a; // Created at program start  
static A a2; // Created at program start  
// a2 not visible outside of current translation unit  
void g() {  
static A a3; // Created first time g() is called  
}
```

## function-static lifetimes ##
- a static variable in a function is initialized the first time the function runs
	- even if it's called from multiple threads, the language is responsible for making sure it only gets initialized once
	- if the function is never called, the object  is never initialized
	- static duration objects are destroyed in the reverse order in which they are created

## Dynamic duration objects ##
- created and destroyed under the control of the program
	- allow you to write programs whose data structures can be determined at runtime based on user imput
	- lifetimes can be controlled with a `unique_ptr`

- Static storage duration
```
#include <iostream>  
using namespace std;  
struct A {  
A() { cout << "Creating an A object" << endl;}  
};  
A static_a;  
int main()  
{ ... }
```

- the above example prints "Creating an A object" before main is run
	- all global and static objects must be constructed before starting the main program
	- one major problem
		- `std::cout` is a global object defined in the C++ runtime library
			- declared by `<iostream>` as `extern ostream cout;`
			- can't guarantee it's initialized first without `ios_base::init()`
				- this initializes the standard streams
## How can we force `cout` to be initialized before `static_a`? ##
- use a static constructor ourselves:
```
#include <iostream>  
using namespace std;  
struct ForceInitialization {  
ForceInitialization() { ios_base::Init(); }  
};  
ForceInitialization forceInitialization;  
struct A {  
A() { cout << "Creating an A object" << endl; }  
};  
A static_a;
```

- since we might need to include `ForceInitialization` in any file that might use `cout` during static initialization, so we should extract it into a header:
```
#ifndef FORCE_INIT_H  
#define FORCE_INIT_H  
#include <iostream>  
struct ForceInit {  
ForceInit() { ios_base::Init(); }  
};  
static ForceInit forceInit;  
#endif
```
- this still isn't right because `ios_base::Init()` will be called once for each source file
	- we only want to call it once period
		- this is how we prevent that:
```
#include <iostream>  
namespace cspp51044 {  
struct ForceInit {  
ForceInit() {  
if(count == 0) {  
count = 1;  
ios_base::Init();  
}  
}  
private:  
static int count;  
};  
static ForceInit forceInit;  
}
```
- this is so useful, it's already part of `<iostream>`
	- as long as we `#include <iostream>` before we call `cout`, we'll be okay
## What if we want our object to outlive the automatic scope? ##
- we might want our dynamic object to be longer lived than the `unique_ptr` that manages it
	- just transfer ownership to a new `unique_ptr`
		- `up2 = move(up);`
		- lets us chain into new scopes while always maintaining an owner of the object