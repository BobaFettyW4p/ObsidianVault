if a method of a template class isn't called, then it isn't compiled
- helpful, buy why isn't a static member never compiled?

CWhy - good way to analyze error messages

Sometimes a method needs a reference to the object it's a part of
- \*this
Inheriting constructors
- we can inherit everything but constructors from the common implementation even though they do the right thing
	- the constrcutors don't have the same names, they are named after the class
		- can make it painful to build lightweight wrapper classes on top of others

Concepts are a C++ template feature, which allow us to constrain templates
- algorithms typically require a callable


C++20 allows us to create a "prototype" for a template

SFINAE
- Substitution Failure is Not an Error
	- when creating candidate overloads for a function, invalid substitutions in a template should mean it's not considered a possible overload
	- No compile error occurs even though the template failed to compile
	- writing SFINAE code, use std::enable_if to make it suck less

constexpr
- allows you to do computations at compile-time
- Normally, we think of programming as a way to write code that runs at runtime
	- increasingly common to take place at compile time instead
- a constant expression is one that can be evaluated at compile-time
	- since templates and performance are so important, we would like to be able to create out own constant expressions
- constexpr has its limitations
	- uninitialized declarations
	- static or thread_local declarations
	- modification of objects that were not created in the function
	- virtual methods
	- non-literal return types or parameters

const v constexpre
- easy to confuse
	- constexpr means the value is known at compile time
	- const means the value will not be changed
		- if you have a const object, you can only call const methods on it

can overload methods on constness

decltype
- sometimes you don't know what type an expression is
	- decltype gives you the type of X
		- Matrix<decltype(x), 1, 2> m = { {x,x} };

Exceptions and RAII
- When something goes wrong, throw an exception
	- ensure they're not inadverdently ignored
- Can throw an exception with throw
	- usually should inherit from std::exception
- can catch an exception with try/catch
- C++20 introduced std::expected that builds on previous code

Why do exceptions break explicit resource management?
- exceptions make it really hard to manage resources and memory
- exceptions in a code block can prevent code that releases resources from being ran, which keeps them active
	- memory leaks

In general, C++ programmers who understand the C++ object lifestyle spend very little time on memory management

RAII
- Resource Acquisition is Initialization
- uses automatic duration objects to manage the lifetimes of dynamic duration resources
	- ensure an automatic object's destructor performs needed cleanup

An object's destructor is always called at the end of it's life
- std::unique_ptr's destructor destroys the object it's managing and releases the memory

Sometimes you want an object to outlive the object that manages it
- move ownership to another RAII object

concurrency makes resource management difficult

constructing an object of type std::thread launches a new thread, running the callable given in the argument

the simplest way to protect shared data is with a std::mutex (mutual exclusion)
- accessing the same data from multiple threads without using synchronization is called a data race error
C++ includes a handy RAII class std::lock_guard
- std::scoped_lock
	- can lock any number of locks without deadlocking

best practice is to use RAII to write safe code
- RAII can cleanup memory management

shared_mutex can be acquired in shared ownership mode or unique ownership mode

Reader-Writer lock
- multiple threads can read from a data structure at the same time as long as no thread is modifying it
- if a thread is modifying the data structure, it should acquire sole ownership so no thread sees the data in a partially modified state
Lock ordering
- if you want to avoid deadlocks, you want to acquire locks in the same order
	- best practice is to document an order on your locks and always acquire them consistent with that order
		- ThreadSanitizer

std::scoped_lock acquires multiple locks at the same time and guarantees there is no deadlock
- tries releasing locks and acquiring in different orders until no deadlock occurs

thread arguments

HW

write a thread-safe stack using locks
- push and pop
- multiple threads can concurrently do things
- for extra credit, additional functionality, initializer list constructor, etc.
- make_unique



