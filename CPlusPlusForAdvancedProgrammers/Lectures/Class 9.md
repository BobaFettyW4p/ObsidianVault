if `std::atomic` runs on hardware that doesn't support atomic operations, the processor will typically create locks underneath the hood
- `std::atomic_flag` doesn't need these, will always be lock free

- `test_and_set()` flips an `std::atomic_flag` to $true
	- atomic flags can only go from false to true
		- only way to maintain thread safety - can't go from $false to $true and back to $false again
			- need mutexes to do that

read more about "cyclomatic complexity"
- a software metric used to measure the complexity of a program
	- counts its decision points
		- quantifies the number of linearly independent paths through a programs source code

# The Solid Principles #
**Single Responsibility Principle**
- how often do you see a class that has members organized into subsets?
	- these are separate pieces pulled together by association
	- what if we only wanted to do one?
		- not without breaking existing code
			- or without breaking the Open-Closed principle
		- a better way is to give each piece it's own class (with a class to join them together)
		- this also applies to functions
			- each function should only do one thing
				- potentially no longer than 10 lines long
				- big hazard: it can be hard to share state, and may necessitate larger functions to accomodate
```
void myFunction(Quux q) {  
// Locals  
Pass p(1);  
Bar b(p);  
Blah l(q);  
Frabulator f = setupFrabulator(b, p, l, q);  
loopThroughSneetches(f, b, p, l, q);
...
```

- functions can be restructured as functors for better organization
```
struct myFunctionHelper {  
myFunctionHelper(Quux q) : q(q), l(q) {}  
Quux q;  
Pass p(1);  
Bar b(p);  
Blah l;  
// Methods now have access to variables  
Frabulator setupFrabulator() { ... }  
void loopThroughSneetches() { ... }  
int operator() {  
Frabulator f = setupFrabulator();  
loopThroughSneetches(f);  
...  
};  
int myFunction(Quux q) { return myFunctionHelper(q)(); }
```
- good choice for very complex functions and supports powerful features like virtual functions and multiple entry points, but may be high friction in simpler cases
	- another option is to use lambdas with capture lists
		- good for "goldilocks" cases where a class would be overkill but still needed
- C++ doesn't have local functions per se, but you can get the same organizing effect with local lambdas:
```
int myFunction(Quux q) {  
Pass p(1);  
Bar b(p);  
Blah l;  
auto setupFrabulator = [&] { ... }  
auto loopThroughSneetches = [&](Frabulator f) { ... }  
Frabulator f = setupFrabulator();  
loopThroughSneetches(f);  
...  
};
```
- good for cases where a class is overkill but multiple responsibilities risk a "spaghetti" function

## Open/Closed Principle ##
- software constructs should be open for extension but closed for modification
	- open for extension
		- allows addition of new capabilities over time
	- closed for modification
		- don't break existing client code
- Inheritance and virtuals
	- inheritance and virtual functions are a great way to extend classes
		- as are the template equivalents we discussed above
			- not quite enough
				- does not cover common use cases for extensions
	- The problem: client-side extension
		- suppose you have a class hierarchy, and you wish the classes had a virtual method specific to the needs of your application
			- it probably doesn't since the class designer doesn't understand your application
			- you may not be able to add them
				- they're not your classes
				- the virtuals you want to apply only apply to your class, and it breaks encapsulation to clutter up an interface with the particulars of every app that uses them
		- The Visitor Pattern is a way to make your class hierarchies extensible
			- suppose I wished the `Animal` class had a `lifespan()` method but the class designer did not provide one
			- make a visitor class that can be overriden:
```
struct AnimalVisitor {  
virtual void visit(Cat &) const = 0;  
virtual void visit(Dog &) const = 0;  
};
```
- add an "accept" method to each class in the hierarchy:
```
struct Animal {  
virtual void accept(AnimalVisitor const &v) = 0;  
};  
struct Cat : public Animal {  
virtual void accept(AnimalVisitor const &v)  
{ v.visit(*this); }  
/* ... */  
};
```
- next, create a visitor that implements the methods you wish were there:
```
struct LifeSpanVisitor  
: public AnimalVisitor {  
LifeSpanVisitor(int &i) : i(i) {}  
void visit(Dog &) const { i = 10; }  
void visit(Cat &) const { i = 12; }  
int &i;  
};
```
- now, you are able to get the `lifespan` of the `Animal` created above:
```
int years;  
a->accept(LifeSpanVisitor(years));  
cout << "lives " << years;
```

- Best practice: if you are designing a class hierarchy where the best interface is unclear, add an `accept()` method as a customization point

## Liskov Substitution Principle ##

![[Pasted image 20250307223346.png]]
- in English: if there are a list of properties about a type, and all of those properties are also true about a second type, you can consider the second type an example of the first
	- and you can use it like once
- inheritance is one way of modeling subtyping
- A `Deer` isA `Animal`
	- one would expect anything that is true about animals is true about deer
	- if `Animal` is a concept instead of a base class, we have seen that the same is true
	- one other benefit of concepts is that inheritance only inherits methods, but concepts can specify arbitrary properties
		- Tradeoff: efficient vs. dynamism
![[Pasted image 20250307223611.png]]

## Interface Segregation Principle ##
- no code should depend on methods it doesn't use
	- often a motivation for using abstract base classes\
-  suppose there is a fat `Job` class that has a bunch of methods relevant only to print jobs and other methods only relevant to stapling jobs
	- if the code for stapling takes a `Job`, it will needlessly only work with `Job`s that also know about printing
		- this can be handled with OO
			- i.e. a concrete `Job` class implements `PrintJob` and `StapleJob` methods
				- this can be taken too far and become needlessly complex
		- concepts also handle this nicely
			- the stapling code can only require what it needs to staple without exploding type hierarchy
				- can also take this too far
					- incoherent set of of functions that each make different requirements of each job that is passed in
		- good reminder that architecture is more art than science

## Dependency Inversion Principle ##
> "All programming problems can be solved with an additional layer of misdirection"

> "the most flexible systems are those in which source code dependencies refer only to abstractions, not to concretions"

- suppose you have a thumbnail service class that looks for pictures in S3 folders:
```
class ThumbnailService {  
S3Folder inputFolder;  
...  
};
```
- it is coupled with the concrete S3 service instead of an abstract idea of a storage service
	- the abstract idea would be sufficient for this use
- the indirection can be introduced either through inheritance/virtuals or template/concepts
	- performance/dynamism tradeoff
- Solving with inheritance and virtuals:
```
Class S3Folder : public Folder { ... };  
class ThumbnailService {  
ThumbnailService(Folder &inputFolder)  
: inputFolder(inputFolder) {}  
Folder &inputFolder;  
...  
};
```
- Solving with templates and concepts:
```
template<Folder F> // Folder is a concept  
class ThumbnailService {  
Folder &inputFolder;  
...  
};  
ThumbnailService<S3Folder> ts;  
ThumbnailService ts2(myS3Folder); // CTAD
```

# Type Erasure #
- you would often want to use templates with different types
	- your use case needs actually classes, not templates
		- previously discussed wanting a fixed type to put in a vector
		- type erasure lets you store a variety of types in a non-template class
- `std::any`
	- uses type erasure to store anything at any time
	- use `any_cast` to get it out
```
any a = 7;  
cout << any_cast<int>(a);  
a = "foo"s;  
cout << any_cast<string>(a);
```

- `std::function`
	- can contain any callable
		- how can it store something of variable type?
			- it does this by erasing the type
**3 parts to type erasure**
- The class
	- non-template class that holds various types
	- does have a constructor or a template assignment operator
```
struct Erased {  
template<typename T> Erased(T&&);  
template<typename T>  
Erased& operator=(T&&);  
};
```
- The concept
	- might not want to store arbitrary types
	- the concept explains what interface you want to support
	- declare a nested Concept class with a pure virtual interface:
```
struct Erased {  
template<typename T> Erased(T&&);  
template<typename T>  
Erased& operator=(T&&);  
struct Concept { virtual void foo() = 0; };  
};
```
- The model
	- class template that stores the actual object (of unknown type) and delegates calls
```
struct Erased {  
template<typename T> Erased(T&&);  
template<typename T>  
Erased& operator=(T&&);  
struct Concept { virtual void foo() = 0; };  
template<typename T>  
struct Model : Concept {  
T val;  
Model(T&& val) : val(val) {}  
void foo() override { val.foo(); }  
};  
};
```

- store and access the type-erased field
```
struct Erased {  
template<typename T> Erased(T&& t)  
: valp(make_unique<Model<T>>(t)) {}  
template<typename T>  
Erased& operator=(T&& t)  
{ valp = make_unique<Model<T>>(t); }  
struct Concept { virtual void foo() = 0; };  
template<typename T>  
struct Model : Concept {  
T val;  
Model(T&& val) : val(val) {}  
void foo() override { val.foo(); }  
};  
void foo() { val->foo(); }  
unique_ptr<Concept> valp;  
};
```

- Let's use it:
```
struct A { void foo(); };  
struct B { void foo(); };  
int main()  
{ Erased e(A());  
e.foo(); // Calls A's foo  
e = B();  
e.foo(); // Calls B's foo  
}
```
- this works even though `A::foo` and `B:foo` are not virtual
	- the virtualness is in the `Model` and the `Concept`

# New and Old Best Practices #
## Best practices for default members ##
- if you do nothing, the compiler will define the following for you implicitly
	- default constructor - no arguments
	- copy constructor - copies all bases and members
	- copy assignments - assigns all bases and members
	- destructor - destroys all bases and members
	- move constructor - moves all bases and members
	- move assignment - move assigns all bases and members
- sometimes the default definitions aren't what you want
	- classes designed to be inherited should have a virtual destructor
	- sometimes we need a deep copy
	- there are best practices to keep this consistent

## The rule of 3 ##
- if you define any of copy constructor, copy assignment operator, or destructor, then define them all
	- the logic is: if the copy constructor has custom behavior...
		- `btree b2(b1); // deep copies`
	- ... the copy assignment should also
		- `b3 = b2; // should also deep copy`

## The Rule of big 2 ##
- even if you have your own copy constructor/assignment, you may not need your own destructor as long as you use RAII

## The Rule of 5 ##
- if you define a copy constructor, then move constructors is not created by the compiler
	- the compiler thinks that if copying every member isn't the right way to copy, then it's unsafe to assume moving every member is the right way to move
		- generally a safe assumption and non harmful
- if you use the rule of 3, think hard about whether you should define move constructors and move assignment operators or if you're happy with them just being copies

## Guard against multiple inclusion ##
- if the same is header is included multiple times, you may get multiply-defined symbol errors
	- this can be avoided via the preprocessor:
```
#ifndef FOO_H  
# define FOO_H  
...  
#endif
```
- in addition, always put headers in a namespace:
```
#ifndef FOO_H  
# define FOO_H  
namespace mpcs51044 {  
int f();  
...  
}  
#endif
```
- and never "use" a namespace in a header
	- leaks the entire namespace to any file that includes a header
		- eg. in a header file, say `using std::accumulate;` instead of `using namespace std;`
			- or explicitly call `std::accumulate` without a `using` statement at all
	- it's okay in a .cpp file

- prefer the C++ versions of C headers
	- `<cstudio>` over `<stdio.h>`
	- the C versions will sort of work, but the C++ will more properly define signatures, so overload resolution, type-checking, etc. will be more robust
		- if there is a C header with no C++ specific version, of course use the C version
			- `unistd.h`
- understand the different between virtual and non-virtual
	- virtual methods are based on the dynamic type of the object
	- non-virtual methods use the static type
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

- prefer C++ style casts to C style casts:
```
A *a = (A *)&b; //bad
A *a = dynamic_cast<A *>(&b);
```


## Where should const go? ##
- `const int` and `int const` mean the same thing
	- but the correct answer is `int const`

## Use const appropriately ##
- const methods should be const
- const & arguments should be const
- the `const` keywork should go after the type:
```
class A {  
public:  
void f(int const &i) const;  
};
```
- ignoring const is no longer an option:
```
int seven() { return 7; }  
void pr_int(int &i) { cout << i; }  
void pr_int_const(int const &i) { cout << i; }  
pr_int(7); // Error  
pr_int(seven()); // Error on newer compilers  
pr_int_const(seven()); // OK
```

## use `nullptr` instead of 0 to indicate a null pointer ##
- `nullptr` is of type `nullptr_t`
	- automatically converts to pointer types (and bool)
```
void f(char *) { /* ... */ }  
void f(int) { /* ... */ }  
f(0); // OK. Calls f(int)  
f(nullptr); // OK. Calls f(char *)
```
- always prefer type-correct `nullptr` over type-incorrect `0` or `NULL`
	- avoids calling the wrong function/method

## Define symmetric binary operators as global functions ##
- don't use member form of `operator+()`
	- both arguments should be treated the same
- do define `operator+=()` as a member
	- the sides matter
		- we don't want to `+=` to try to assign to a compiler-generated temporary

- templates are great because they deduce the type and create appropriate overloads, but they will occasionally not create the overload you want
- if you're accumulating doubles with `std::accumulate` use an initial value of `0.0` instead of `0`
	- otherwise, you'll accumulate integers instead, i.e.
```
WRONG
double dp[] = { 0.1, 0.2, 0.3 };  
cout << accumulate(dp, dp + 3, 0); // this prints "0"

CORRECT
double dp[] = { 0.1, 0.2, 0.3 };
cout << accumulate(dp, dp + 3, 0.0) // prints 0.6, which is probably what you want
```
- you always need to think of the types inferred by templates:
```
struct maxlenftn {  
maxlenftn() { maxlen = 0; }  
void operator()(string s) {  
maxlen = max(maxlen,s.size());  
}  
string::size_type maxlen;  
};  
int main() {  
vector<string> names{"Spertus", "Lemon", "Golden", "Melhus“};  
maxlenftn maxf;  
for_each(names.begin(),names.end(),maxf);  
cout << maxf.maxlen << endl; //prints 0
return 0;  
}
```
- this (unexpectedly) prints 0
	- why?
		- because `maxlenftn` is passed by value, a copy of it is updated, leaving the original unchanged
			- need to pass `maxlenftn` by reference so this `maxlenftn` is able to be updated

## Beware of dependent base classes ##
- what does this print?
```
#include <iostream>  
using namespace std;  
int f() { return 0; }  
template<class T>  
struct C : public T {  
C() { cout << f() << endl; }  
};  
struct A {  
int f() { return 1; }  
};  
int main()  
{  
C<A> c;  
}
```
 - surprisingly, depends on the compiler
	 - Microsoft Visual C++ used to print `1`
	 - g++ prints `0`
		 - correct
	- `T` is a "dependent base class"
		- a dependent base class is one that depends on the template parameter
		- symbols are not looked up in dependent base classes, so templates are not surprised by unexpected inheritance
			- to see symbols in a dependent base class, reference it explicitly:
```
struct C : public T {  
C() { cout << T::f() << endl; }  
};

//alternative
template<class T>  
struct C : public T {  
using T::f;  
C() { cout << f() << endl; }  
};

//alternative #2
template<class T>  
struct C : public T {  
C() { cout << this->f() << endl; }  
};

//alternative #3 - if you want the global symbol
template<class T>  
struct C : public T {  
C() { cout << ::f() << endl; }  
};
```

- watch out for method hiding:
```
struct B {  
void f(bool i) { cout << "bool" << endl; }  
};  
struct D : public B {  
// Fix with "using B::f"  
void f(int b) { cout << "int" << endl; }  
};  
int main()  
{  
D d;  
d.f(true); // Prints "int"  
}
```


## Use `override` and `final` to indicate intent ##
```
struct Base {  
virtual void func() = 0;  
virtual void mispelledFunc() = 0;  
};  
struct Derived : public Base {  
virtual void func() override final {}  
// This will give a useful error  
// because we aren’t actually  
// overriding  
virtual void misspelledFunc() override {}  
};  
struct MostDerived : public Derived {  
// Error! Can’t override final  
virtual void func() { /*...*/}  
};
```
- this catches a lot of "method hiding" errors

## Throw exceptions by value and catch them by `const &` ##
```
struct MyException : public exception {  
MyException(string s)  
: myS("My "+s), exception(s) {}  
virtual char const *override what() {  
return myS.c_str();  
}  
string myS;  
};  
void f() {  
try {  
throw MyException("foo");  
} catch (exception e) { // Bad!  
//} catch (exception const &e) { // Better  
cout << e.what(); // May crash due to slicing  
}
```

## Never have a destructor throw an exception ##
- does the following catch "In A" or "in f"?
```
struct A {  
~A() { throw runtime_error("In A"); }  
};  
void f()  
{ try {  
A a;  
throw runtime_error("in f");  
} catch (exception const &) {  
}  
}
```
- no good answer
	- the runtime will just call `std::terminate` to end your program

## use virtual destructors when you inherit ##
```
class A {  
public:  
// virtual ~A() = default;  
};  
class B : public A {  
public:  
~B() { ... }  
};  
A *ap = new B;  
delete ap; // Doesn’t call B’s destuctor
```

## Prefer templates to macros ##
- e.g. `min` should be a template but Microsoft Visual C++ once defined it as a macro:
```
#define bad_min(x, y) x<y? x: y;  
int i = 3;  
bad_min(5, i++); // increments i twice!  
2*bad_min(3, 5); // Returns 5!
```
- it's even hard to figure out why they fail
	- wouldn't have been an issue with a function template

## Don't make tricky assumptions about order of evaluation ##
```
struct S {  
S(int i) : a(i), b(i++) {  
f(i,i++) // Undefined behavior  
}  
int b;  
int a;  
};
```

## Remember that primitive types have trivial "constructors" ##
```
void  
f()  
{  
int i;  
/* int i{}; // Fix with */  
cout << i; // i contains garbage  
}
```

## Don't return a reference/pointer to a local variable ##
```
int &  
f()  
{  
int i = 3;  
return i; // Bad!  
}
```
- once the local variable ceases to exist, the local variable will be destructed

## Prefer range member functions to their single-element counterparts ##
- item 5 of Meyer's Effective STL
- given two vectors, `v1` and `v2`, what's the easiest way to make `v1`'s contents be the same as the second half of `v2`'s?
	- don't worry about whether `v2` has an odd number of elements
Worst way (but very common):
```
vector<Widget> v1, v2  
...  
for(vector<Widget>::const_iterator ci  
= v2.begin() + v2.size()/2;  
ci != v2.end();  
++ci) {  
v1.push_back(*ci);  
}
```
better:
```
copy(v2.begin() + v2.size()/2,  
v2.end(),  
back_inserter(v1));
```
better yet:
```
v1.resize(v2.size() - v2.size()/2);  
copy(v2.begin() + v2.size()/2,  
v2.end(),  
v1.begin());
```
even better:
```
v1.insert  
(v1.end(),  
v2.begin() + v2.size()/2,  
v2.end());
```
best
```
v1.assign(v2.begin() + v2.size()/2,  
v2.end());
```

## Prefer `empty()` to `size()==0` ##
- suppose `l` is a `list<int>`
	- which is better?
		- `if(l.size()==0) { ... }`
		- `if(l.empty()) { ... }`
			- this is preferred
				- calculating `size()` can take a long time

## Always use a smart pointer to manage the lifetime of an object ##
- `unique_ptr` if it only has one owner
- `shared_ptr` if it has multiple owners
	- more generally, use RAII to ensure resources get destroyed when they are no longer needed
```
Foo *fp = new Foo; // Bad  
// stuff  
delete fp; // May be missed if exception occurred
```

## Avoid using `operator new` ##
- the problem with `new A()` is that it returns an owning raw pointer to the new object
	- this violates the previous best practice to always use smart pointers
		- use `make_unique` and `make_shared` instead
			- `auto ap = make_unique<Foo()>; //best`
			

## Use RAII to manage locks ##
- similar to smart pointers for objects, us a scoped locking class whose destructor releases the lock makes sure they are released even when exceptions bypass control flow
	- `std::scoped_lock`
	- manual unlocking code can be bypassed by an exception
		- exactly like raw pointers!
## Lock ordering ##
- to avoid deadlocks, you want to acquire locks in the same order
	- suppose thread 1 acquires lock A and then lock B
	- but thread 2 acquires lock B and then lock A
		- there is a window where thread 1 could own lock A and attempt to acquire lock B, and thread 2 owns lock B and tries to acquire lock A
			- neither will be successful, they will be waiting forever
	- best practice is to document an order of your locks, and then acquire them in that order always
	- `std::scoped_lock` can acquire multiple locks without deadlocking in the event this order needs to be broken

## Memory model best practices ##
- avoid sharing data between threads unless necessary
	- if you do, always use locks and atomics

## Make mutex members mutable ##
```
struct A {  
int f() const {  
lock_guard<mutex> lck(mtx);  
return i + j;  
}  
int i;  
int j;  
// So const methods can lock  
mutex mutable mtx;  
};
```

## Prefer `using` to `typedef` ##
- a program might have the following line:
```
using ConfigVariables =  
map < string, variant<string *, bool *, double *>>;
```
- you could also use this syntax for the same thing:
```
typedef  
map < string, variant<string *, bool *, double *>>  
ConfigVariables;
```
- in general, the first is preferred
	- `typedef` doesn't work with templates
		- it also have trouble with recursive types
			- `using` is just as easy to use, and doesn't have these issues

## Constrain your templates ##
- don't just have `typename` parameters
	- your user will not be protected from using incorrect types for template arguments
		- you'll get an incomprehensible error message...
		- or even worse, a silently incorrect answer
Three different approaches:
```
//bad - silently gives wrong results
template<typename T>  
T gcd(T a, T b) {  
return (b == 0) ? a : gcd(b, a – b*(a/b));  
}

//C++20 Concepts - good
template<integral T>  
T gcd(T a, T b) {  
return (b == 0) ? a : gcd(b, a – b*(a/b));  
}

//Pre-C++20 - Use SFINAE
template<typename T, typename = enable_if_t<is_integral_v<T>>>  
T gcd(T a, T b) {  
return (b == 0) ? a : gcd(b, a – b*(a/b));  
}
```

## Functions that take in text should use `string_view` ##
- a string view can understand the underlying region of memory in both a `std::string` and C string
```
int f(string_view const s);  
string str("foo");  
auto a = f(str) + f("bar") + f("baz"s); // No copying!
```
- under the hood, a `string_view` is just a pointer and a length
	- you can even say `f({"foobar",3})` just to see `"foo"`

## The most vexing parse ##
- sometimes complex types can not only confuse you, they can confuse the compiler as well:
```
ifstream dataFile("ints.dat");  
list<int>  
data(istream_iterator<int>(dataFile),  
istream_iterator<int>());
```
- what does this even do?
```
int f(double d); // Declares function double -> int
int f(double (d)); // Also function double -> int
Foo f(istream_iterator<int>(datafile));  
// Oops! Function istream_iterator<int> -> Foo
int f(double(*p)()); // function double(*)()->int
int f(double p()); // Also function double(*)()->int
// Can always omit parameter name  
int f(double()); // Again function double(*)()->int
Foo f(istream_iterator<int>());  
// Oops. Function istream_iterator(*)() -> Foo
```
- there are a couple ways to fix:
	- could use parenthesis to disambiguate:
```
ifstream dataFile("ints.dat");  
list<int>  
data((stream_iterator<int>(dataFile)),  
istream_iterator<int>());
```
- could be more explicit
```
istream_iterator<int> dataBegin(dataFile);  
istream_iterator<int> dataEnd;  
list<int> data(dataBegin, dataEnd);
```
- this still is pretty messy, is there a better way to do this?

## Uniform initialization ##
- Bjarne Stroustrup wanted to simplify initialization according to the following principles:
	- `{..}` can be used for every initialization
	- `{...}` implies direct initialization
	- `{...}` implies is non-narrowing
```
// Now works fine  
ifstream dataFile("ints.dat");  
list<int>  
data{istream_iterator<int>{dataFile},  
istream_iterator<int>{}};
```
- these principles are not fully achievable, but a good baseline

## Namespace versioning ##
- namespaces are a great way of versioning APIs
	- this means code can say they are using `v1` functions an interfaces
	- we also want to be able to say "I always want to be on the latest version"
```
// MyLib.h  
namespace mpcs51044 {  
namespace v1 {  
int f() { // Original way of calculating f }  
}  
namespace v2 {  
int f() { // Modern efficient way }  
}  
using namespace v2; // Make v2 the default 

//even though v2 is the default, we can still use the v1 version if it's needed
// MyMissionCriticalProgram.cpp  
#include "MyLib.h"  
auto x = mpcs51044::v1::f();

// MyYoloProgram.cpp  
#include "MyLib.h"  
auto x = mpcs51044::f(); // Latest version
}
```

