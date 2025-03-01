
- godbolt.org
	- online compiler

Michael Spertus
- mike@spertus.com

**Course Info**
Mike - teaches the Generative AI and Big Data Application Architecture course

*The best way to learn C++ is to join the standards committee*
- costs ~2k

**Useful Resources**

- language standard - https://eel.is/c++draft/
- http://isocpp.org/
	- public facing community website of the standards committee
- C++ core guidelines
	- isocpp github
	- repository of current C++ best practices
- http://herbsutter.com - convener of the C++ standards committee
- ISO committee website
	- need a password for certain parts, but not all
- http://includecpp.org - group to support diversity in C++
- Online compilers
	- https://wandbox.org
	- https://godbolt.org
- C++ section of StackOverflow
- cppreference.com, /r/cpp

**Useful Books

Bjarne Strousroup - A Tour of C++ 3rd edition
- Programming: Principles and Practices
Nico Josuttis - C++20, The Complete Guide
Anthony Williams, C++ Concurrency in Action

CWhy - useful tool to diagnose error messages

**Weekly Schedule

HW and lecture notes - published on Canvas Tuesday morning
HW due on Monday before class on Gradescope
- no late HW
- if HW is submitted by Thursday midnight, it will be graded with comments by Saturday, and you can resubmit

backwards homework
- if you just teach a feature, very abstract
	- if you struggle on the HW, then you totally understand when you get it on the HW
	- it will always be possible to do HW with the material we have received, but you'll be prepared for the next class

**Discussion Group

- Ed tab on Canvas
- post privately if it's about homework

Grading
- 2/3 HW
- 1/3 final - actually has the biggest impact on your grade

programming the cache?!?!
- Red Panda is a C++ implementation of Kafka
	- 10x as fast, 10x as cheap
	- main difference: C++ is native, can focus on access data in cache and process affinity

huge gaps in C++
- no networking
- No thread pools
- Asynchronour programming broken until C++26
- No sender-receiver
- No reflection
	- no serialization, RPC, etc
- too many things still require unsafe code

**Safety

- C++ isn't safe
	- lets you access memory location directly by address
		- "pointers"
		- doesn't have the safety guarantees of other languages
	- these risks can be alleviated
		- modern best practices
		- there are still many unsafe-by-default constructs
- C++ will become the first tier one language with contracts in C++26
- C++20/23
	- "feels like a new language"

**Characteristics of C++
- Compiled
- Multiparadigm
- Lightweight abstractions
	- supports low-level system programming and high-level abstractions

C++ is a Compiled language
- uses "Ahead of Time" compilation
	- have to manually compile before running
	- can be much faster
	- enables "metaprogramming" the compiler to control code generation

Multiparadigm
- C++ is not an object oriented language
	- but it does support OOP
	- also supports other paradigms
		- standard library prefers compile-time dispatch (templates)

C++ is a lightweight abstraction language
- languages typically fall into a tradeoff between being good for programmers (abstract) and goood for computers (low memory, fast, low level)
- we need both as both loom over programming decisions
	- need to be able to decide as needed
		- can create powerful abstractions with all abstraction "compiled away" using templated
		- no performance penalty associated

Delivering performance and abstraction
- allows low-level manipulation of code and data
- use compile-time computate to generate optimal code
- C++ has no equivalent of scripting or iPython notebooks

how do we copy data from one data structure to another?
- in C, we can copy the underlying memory with `memcpy` to get low-level performance
	- fast, but not abstract
	- not fit for prod programs that are complex and need to be adapted over time
- more complex objects contain other objects all over memory
	- in C, copying a compound object only copies the rooy
		- have to manually write deep copy commands
			- brittle implementation details

C++ classes can be considered the way you create your own types
- copy constructor that teaches you the correct way to copy objects
	- abstract: copy any object with an assignment, independant of implementation
	- lightweight: compiler generates the code, so it's just as efficient of doing it manually

C++ std library provides a standard copy function
- ultimate in abstraction
	- copies from anything to anything else, can do deep copies
	- as abstract as Java
		- can copy any collection to any other collection w/ no change in code
	- as efficient as C
		- compiler uses a mechanism called "template programming" to generate the most efficient code at compile-time
			- will simply use `memcpy` if that is sufficient
				- 800% performance improcement in some cases

Static type safety and inference
- Python are unsafe in a different way
	- any variable can hold any type of object
		- "dynamic typing"
		- can change over the course of a program and lead to unsafet
			- C++ does not allow this
				- types are checked during compilation
					- Static type-safety
					- compiler will not let you misinterpret the type of an object

"Programmers working in richly typed languages often remark that their programs tend to 'just work' once they bypass the typechecker"
- having to write out all types is a lot of friction
	- Solution: Inference
		- C++ can infer the type without being explicitly mentioned
			- benefits of static typing without headaches
				- templates are based on inferring types

Exceptions, Excepted, and RAII
- proper error handling and clean up
	- estimated 40% of dev time
- variety of abstractions to handle this





Starting from "Hello World"

- don't need to use a specific compiler or IDE
	- Advantages:
		- get comfortable and use your preferred environment and toolchain
			- learn how portable C++ code is
				- C++ is not fully standardized
	- Disadvantage:
		- you will have to install and configure it according to your choices

What compiler should you use
- any modern compiler with support for C++20
	- Clang, g++, and Visual C++ all work
		- may need to set a `-std=c++20` flag
	- VSCode, CLion, godbolt.org


CMake
- you are free to build however you want
	- CMake is the most common way to manage the complexity of building
		- "meta build" system for any compiler and operating system
		- CMake tends to introduce it's own complexity

CMakeLists.txt
- you describe a project with a \CMakeLists.txt


{fmt}
- widely used open soruce library that implements format in C++ when the compiler doesn't
	- a little ugly to download a "standard" library, but worht it
		- many C++ projects already doing this
- download it from github fmtlib/fmt
- use C++ package manager
- modify top fo the file
	- `#include<fmt/format.h`
	- `using namespace fmt;`



The preprocessor
- #include and #define are commands to the C++ preprocessor, that do simple cut and paste on text
- #include `foo.h` cuts and pastes foo.h directly into the including file
- #define x 7 replaces all occurences of token X with the toke 7
- most modern languages do not include a pre-processor because they cause problems
	- preprocessor definitions ignore namespaces
	- each include source file balloons to tens of thousands of lines
	- how should an IDE refactor?
	- C++ has been adding features to eliminate the need to use the preprocessor

C++ lets you separately compile and link libraries
- can speed compilation, complexifies build process
- {fmt} can be used as a linkable library, or in header only mode, where the preprocessor pastes in all the code

C++ has a variety of ways of defining and initializing variables
- will be covered
- `int i=5` //i is an int initialized to 5
- i=7 // i is now 7
- i="Hello; //this is an error, C++ is a statically rtyped language
- auto j=3; //j is an int

why do they call it a double?
- floating points were a big problem for computers until relatively recently
	- when C/C++ were being invented, floats were a lot smaller to be able to do floating point arithmetic
	- there are many more gates on a chip, to create a double precision floating point
		- modern computers can process these in much faster times
			- progression to half-width floating points
				- modern neural nets don't need to be that precise

C++ is statically typesafe, largely rype inferenced
- powerful generic mechanism known as templates
	- give simplicity of untyped types with the safety of compiled-time type calidation
	- major theme in C++, still a work in progress




