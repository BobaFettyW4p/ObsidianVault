if `std::atomic` runs on hardware that doesn't support atomic operations, the processor will typically create locks underneath the hood
- `std::atomic_flag` doesn't need these, will always be lock free

- `test_and_set()` flips an `std::atomic_flag` to $true
	- atomic flags can only go from false to true
		- only way to maintain thread safety - can't go from $false to $true and back to $false again
			- need mutexes to do that

read more about "cyclomatic complexity"

**The Solid Principles**
- Single Responsibility Principle
- Open/Closed Principle
- Liskov Substitution Principle
- Interface Segregation Principle
- Dependency Inversion Principle

Single Responsibility Principle
- how ofen do you see a class that has members organized into subsets?
	- separate pieces pulled together by association
	- what if we only wanted to do one?
		- not without breaking existing code
		- a better way is to give each piece it's own class (with a class to join them together)
		- this also applies to functions
			- each function should only do one thing
				- potentially no longer than 10 lines long
				- big hazard: it can be hard to share state, and may necessitate larger functions to accomodate
		- functions can be restructured as functors for better organization
			- gives functions better access to local variables
			- good choice for very complex functions, but may be high friction in simpler cases
		 - another option is to use lambdas with capture lists
			 - good for "goldilocks" cases where a class would be overkill but still needed

Open/Closed Principle
- software constructs should be open for extension but closed for modification
- allow addition of new capabilities over time, but dont' break existing client code

Inheritance and virtual functions are a great way to extend classes
- templates are as well

Visitor pattern
- way to make class hierarchies extensible
	- create a visitor class that can be overriden
	- add an `accept` method for each class in the hierarchy

Best practice
- if you're designing a class hierarchy where the best interface is unclear, add an `accept()` method to let people build their own interfaces

Duck typing
- isA
	- inheritance is a model of it
	- duck typing gives a different notion

Templates have always been duck typed, they are used for compile-time dispatch
- OOP is used for dynamic dispatch
- since duck typing is flexible and forgiving while being statically typesafe, people want to use duck typing as an alternative to inheritance
	- variants let us do this
		- `using Animal = variant<Cat, Dog>`
		- can you call a method on a variant?
			- yes
				- almost as fast as a template, as dynamic as traditional OO
- malleable typing
	- virtual functions need to exactly match what they override
		- can't give a default argument to methods in derived classes
			- can be done in variants
				- notation much uglier than calling a virtual function
					- also reduces type safety
					- variants always use as much space as the biggest type
	- this is ugly and not well-known, only preer variant-based polymorphism over virtuals or templates when there is a clear benefit

Liskov Substitution Principle
- list of properties about a type
	- if they're all true about a second type, it's an example of the first type and you can use it like one

Interface Segregation Principle
- no code should depend on methods it doesn't use
- often a motivation for using abstract base classes
- i.e. a concrete `Job` class implements `PrintJob` and `StapleJob`
	- Concepts are a good solution

Dependency Investion principle
- "All programming problems can be solved with an additional layer of misdirection"

**Type Erasure**
- you would often want to use templates with different types
	- your use case needs actually classes, not templates
		- type erasure lets you store a variety of types in a non-template class
- `std::any`
	- uses type erasure to store anything at any time
	- use `any_cast` to get it out
- `std::function`
	- can contain any callable
		- it does this by erasing the type

3 parts to type erasure
- The class
	- non-template class that holds various types
	- does have a constructor
- The concept
	- might not want to store arbitrary types
	- explains what interface you want to support
- The model
	- class template that stores the actual object (of unknown type) and delegates calls

REVIEW
Best practices for default members
- things that are implicitly defined
	- default constructor - no arguments
	- copy constructor - copies all bases and members
	- copy assignments - assigns all bases and members
	- destructor - destroys all bases and members
	- move constructor - moves all bases and members
	- move assignment - move assigns all bases and members
sometimes the defualt definitions aren't what you want
- you should write your own

The rule of 3
- if you define any of copy constructor, copy assignment operator, or destructor, then define them all

rule of big 2
- if you're using RAII, the default destructor will probably work
rule of 5
- if you define a copy constructor, the move constructor is not created by thec compiler
	- if you can't just copy every member, it will assume they can't just be moved
Guard against multiple inclusion
- if the same is header is included multiple times, you may get multiply-defined symbol errors
	- `#ifndef FOO_h` `endif`
always put headers in a namespace

never "use" a namespace in a header
- leaks the entire namespace to any file that includes a header
- it's okay in a .cpp file

prefer the C++ versions of C headers
- `<cstudio>` over `<stdio.h>`

understand the different between virtual and non-virtual
- virtual methods are based

prefer C++ style casts to C style casts


where should const go - it should be on the right

use const appropriately
- const methods should be const
- const & arguments should be const
- it shoudl go after the type
	- int const i;

use `nullptr` instead of 0 to indicate a null pointer

define symmetric binary operators as global functions
- don't use member form of operator+()
	- do define operator+=() as a member
		- the sides matter

templates are great becuase they deduce the type and create appropriate overloads, but they will occasionally not create the overload you want


user override and final to indicate intent

never have a destructor call an exception

prefer templates to macros
don't make tricky assumptions about order of evaluation

remember that primitive types have trivial "constructors"
- initialize with garbage

do not return a reference/pointer to a local variable
- once it ceases to exist, the local variable will be destructed

prefer range member functions to their single-element counterparts

prefer `empty()` to `size()==0`
use RAII to ensure resources get destroyed when they are no longer needed

avoid using `new` use `make_unique`

use RAII to manage locks
- a scoped locking class whose destructor releases the lock makes sure they are released even when exceptions bypass control flow
	- `std::scoped_lock`

to avoid deadlocks, you want to acquire locks in the same order
- all threads should acquire A before B before C
	- `std::scoped_lock` can acquire multiple locks without deadlocking

Memory model best practices
- avoid sharing data between threads unless necessary
	- if you do, always use locks and atomics
- make mutex members mutable
	- gives const methods the ability to lock
prefer `using` to `typedef`
- `typedef` doesn't work with templates and has issues with recursive types

constraint templates
- don't just have `typename` parameters
	- Concepts is the best option, but requires C++20
		- SFINAE in pre-C++20

functions that take in text should use `string_view`

namespaces are a great way of versioning APIs

**REVIEW OF IMPORTANT CONCEPTS**
- C++ is partially a standard
	- many aspects are not standardized
		- designed to match the hardware they run on
		- avoid nonstandard vendor-specific extensions
- C++ is a lightweight abstraction language
	- programming languages are either good for programmers (abstract)
