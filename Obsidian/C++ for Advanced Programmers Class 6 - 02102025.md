hardware concurrency v software concurrency
- 100 threads with only one core, then it means the threads have to share the same core, one at a time
	- not actually getting concurrency

don't use std::ostringstream, use std::format

if you don't want to join a thread, but want it to run to completion, you detach() it

final exam

questions similar to the homework, large code review
- don't use a compiler, pencil and paper exam where you use a code editor
- exam will be in person but online

Distributed counter
- a counter that can be incremented or read by any thread
	- seems simple, has a lot of complexist

multiple threads can share a shared mutex, but only one can write
- "shared" mode v. "exclusive" mode
- compared to a std::mutex, where only one thread can take the mutex at once

you can overload operator++ with int as an argument

Amdahl's Law - the more sequential tasks in a program, the less it benefits from parallelism

Cache Conscious Programming
- accessing main memory can take processors hundreds of cycles
- processors use high-speed caches to maintain local copies of data
- if another processor needs to RW that memory, other processors have to flush or invalidate cached copies of that memory

put independant objects on independent cache lines
a single memory location can only be mapped to a single cache line

how does c++ handle cache-conscious programming?
- you can make an educated guess, but they're brittle
- you can put stuff on the same/different cache lines
	- std::hardware_destructive_interference_size
		- if objects are further than it, different lines; otherwise they "should" end up on the same line


Cache-Conscious Programming
- objects or fields that are accessed independantly should be aligned and padded so they end up on different cache lines
- keep read-only data separate from data that is modified frequently
- When possible, split an obkect into thread-local pieces
	- a counter used for statistics could be split into an array of counters, one per thread, each on a different cache line
	- The split counter allows each thread to update its own replica without causing cache coherence traffic
- If a lock protects frequently-modified data, keep the lock and data on distinct cache lines, so threads trying to acquire the lock do not interfere with access to the data
- If a lock protects uncontended data, try and keep the lock and the data on the same cache lines, so acquiring the lock will load data into the cache
- If a struct contains a large chunk of data whose size is divisible by a high power or two, consider separating it out of the class and holding it with a unique_ptr to avoid Ghostscript problem
- use a profiling tool like VTune to identify cache bottlenecks

CTAD
Class Template Argument Deduction
- usually with classes, you have to specify the template arguments
- for functions, specifying the template parameter is optional
	- let the compiler deduce teh parameter type
	- you only need to give the template argument if you want something other than waht is deduced
- since creating an object calls the constructor, the compiler can deduce template arguments passed to the constructor
- has advanced features like deduction guides
Mov semantics
- pass by value void f(Xx);
- pass by reference void f(X &x);
- pass by move void f(X &&x)
- most languages only have one choice, C++ hsa all three
x = move(y)
- this means that the value of X is the same as the value y started at
- this can be done with ordinary copying - x=y;
- the difference is that y is allowed to be changed by the move
	- subsequent code should not use the value of y

since a move can raid the old object for parts, it's more efficient
- movign a tree only requires moving the root

Semantic argument to move:
- some classes don't make sense to copy and therefore have deleted copy constructors
	- if you copy a unique_ptr, the managed object would be owned by two unique_ptrs
		- move fixes this
- objects are often much cheaper to "move" than copy
	- some objets (unique_ptr) can be moved but not copied
C++ lets you use ... to indicate a "pack" of template parameters

Low-level and Systems Programming
Pointers
- low level way to refer to a location in physical memory
	- for the most part, use references and higher-level lightweight abstractions like unique_ptr
		- some cases aren't covered:
			- a reference always refers tot he same object, but doesn't own it - can't be "unbound"
			- a unique_ptr can  change what it manages, and can be unset, but it owns any object it manages

C++ sometimes forces you to use a pointer:
- this is a pointer
	- better to use *this
- main uses to points to pointesr to pass command line arguments
	- this is to maintain C compatibility

When do we need pointers?
- low level programming
	- C++ is a systems programming language
		- may need to access memory directly
- Advanced memory management
	- nearly all can be handled with unique_ptr and shared_ptr
	- are situations they don't cover

Pointesr to a type conatin the address of an object of the given type
A *ap = new A();

ap contains the address of an object of type A in memory
new A() constructs an object of type A in memory and returns its address
new A() has the same relation to A *

pointers can dereference with *
A a = *ap;

-> is an abbreviationm for (star_)

raw pointers do not have destructors, and do not manage the lifetime of the object it points to
If a pointer is not pointing to any object, you should make sure it is nullptr

Old C/C++ code uses 0 or NULL to represent an empty pointer
- don't do this, breaks overloading

pointer arithmetic is possible
- better to use std::array or std::vector


C-style string literals are pointers
"foo" is a literal of type char const[4]


pointers to functions
- usually describe a type by how it is used
	- int star ip // means start ip is an int
	- int (star fp) (int, int) // star fp can be called with 2 ints

References allow one object to be shared among different variables
- they are set on creation and never changed
- cannot be null

std::function
- in C++, functions aren't the only thing that can be called
	- function
	- lambda
	- functor
	- member function
- std::function can hold anything callable

operator new is siilar to malloc in that it allocates the requested number of bytes of memory

if you have memory and want to put an object in it, use operator new

try distributed counters

