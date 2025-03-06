
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
- Asynchronous programming broken until C++26
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
- Statically typesafe and type inferenced
- Exceptions, Expected, and RAII

**C++ is a Compiled language
- uses "Ahead of Time" compilation
	- have to manually compile before running
	- can be much faster
	- enables "metaprogramming" the compiler to control code generation

**Multiparadigm
- C++ is not an object oriented language
	- but it does support OOP
	- also supports other paradigms
		- standard library prefers compile-time dispatch (templates)

**C++ is a lightweight abstraction language
- languages typically fall into a tradeoff between being good for programmers (abstract) and good for computers (low memory, fast, low level)
- we need both as both loom over programming decisions
	- need to be able to decide as needed
		- can create powerful abstractions with all abstraction "compiled away" using templated
		- no performance penalty associated

**Delivering performance and abstraction
- allows low-level manipulation of code and data
- use compile-time computation to generate optimal code
- C++ has no equivalent of scripting or iPython notebooks

how do we copy data from one data structure to another?
- in C, we can copy the underlying memory with `memcpy` to get low-level performance
	- fast, but not abstract
	- not fit for prod programs that are complex and need to be adapted over time
- more complex objects contain other objects all over memory
	- in C, copying a compound object only copies the root
		- have to manually write deep copy commands
			- complicated, time-consuming and brittle implementation details

C++ classes can be considered the way you create your own types
- a copy constructor teaches the compiler the correct way to copy objects
	- abstract: copy any object with an assignment, independant of implementation
	- lightweight: compiler generates the code, so it's just as efficient of doing it manually

C++ std library provides a standard copy function
- `std::copy`
- ultimate in abstraction
	- copies from anything to anything else, can do deep copies
	- as abstract as Java
		- can copy any collection to any other collection w/ no change in code
	- as efficient as C
		- compiler uses a mechanism called "template programming" to generate the most efficient code at compile-time
			- will simply use `memcpy` if that is sufficient
				- 800% performance improvement in some cases

Static type safety and inference
- Python is unsafe in a different way
	- any variable can hold any type of object
		- "dynamic typing"/"duck typing"
		- can change over the course of a program and lead to unsafety
			- C++ does not allow this
				- types are checked during compilation
					- Static type-safety
					- compiler will not let you misinterpret the type of an object unless you deliberately try to deceived it
```
var a = "foo"; //Javascript
if (bar())
a = 7; //a might change type
console.log(a.length()); //Legal? who knows?
```

>"Programmers working in richly typed languages often remark that their programs tend to 'just work' once they bypass the typechecker"
> - Benjamin Pierce, *Types and Programming Languages*

- having to write out all types is a lot of friction
	- in comparison, Python is a lot easier to just "hack out" even if they're less suitable for reliable prod systems
	- Solution: Inference
		- C++ can infer the type without being explicitly mentioned
			- benefits of static typing without headaches
				- templates are based on inferring types
```
auto a  = "foo"s; // compiler will deduce a is a string
a = 7; // Ill-formed, will not compiled
```

Exceptions, Excepted, and RAII
- proper error handling and clean up takes ~40% of C development time
	- C++ offers a variety of abstractions to handle this and reduce that time





**Starting from "Hello World"

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

# HelloWorld.cpp
```
#include <iostream>

int
main()
{
std::cout << "Hello, world!\n";
return 0;
}
```


**CMake
- you are free to build however you want
	- CMake is the most common way to manage the complexity of building
		- "meta build" system for any compiler and operating system
		- CMake tends to introduce it's own complexity

**CMakeLists.txt
- you describe a project with a \CMakeLists.txt
- for HelloWorld.cpp:
```
cmake_minimum_required(VERSION 3.5)
set(CMAKE_CXX_STANDARD 20)
project(HelloWorld)

add_executable(hello_world hello.cpp)
```
- to build:
	- `cmake -S . -B ./build` - invokes CMake on `pwd`, searches for the CMakeLists, then creates the build directory at `./build`
	- `cmake --build ./build` - builds the binaries and stores them in `./build` according to the requirements
	- can leverage VSCode to simplify this

**A More Personalized Greeting
```
#include<iostream>
#include<format>
using namespace std;

int
main()
{
string name
cout << "What is your name?";
cin >> name;
cout << format("Hello, {}!\n", name);
return 0;
}
```

- this may or may not compiled based on the library
- `<format>` was added in C++ 20, but not all compilers have implemented it, but there is an alternative...

**{fmt}
- widely used open source library that implements format in C++ when the compiler doesn't
	- a little ugly to download a "standard" library, but worth it
		- many C++ projects already doing this
- download it from git repo  `fmtlib/fmt`
- use C++ package manager
- modify top fo the file
	- `#include<fmt/format.h`
	- `using namespace fmt;`



The preprocessor
- `#include`and `#define` are commands to the C++ preprocessor, that do simple cut and paste on text
- `#include foo.h` cuts and pastes foo.h directly into the including file
	- this technique is used in a lot of standard library implementations
- `#define x 7` replaces all occurrences of token X with the token 7
	- dangerous, should be careful using this
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
- `int i=5 //i is an int initialized to 5`
- `i=7 // i is now 7`
- `i="Hello; //this is an error, C++ is a statically typed language`
- `auto j=3; //j is an int`

why do they call it a double?
- floating points were a big problem for computers until relatively recently
	- when C/C++ were being invented, floats were a lot smaller to be able to do floating point arithmetic
	- there are many more gates on a chip, to create a double precision floating point
		- modern computers can process these in much faster times
			- progression to half-width floating points
				- modern neural nets don't need to be that precise
**Defining functions
```
int square(int n)
{
return n*n;
}
//good, but can only accept ints
double square(double n)
{
return n*n;
}
//this would let us use square() for either ints or doubles
//called a function overload; the compiler is smart enough to infer which should be used in a specific situation

auto square(double n)
{
return n*n;
}
//this can replace both above square() functions; compiler will infer return type based on inputs to square()
```

C++ is statically typesafe, largely type inferenced
- powerful generic mechanism known as templates
	- templates let you give a name to a not yet specified type and allow the compiler to infer that type as needed
	- give simplicity of untyped types with the safety of compiled-time type validation
	- major theme in C++, still a work in progress
```
//function templates

auto square(auto n)
{
return n*n;
}
int main()
{
return square(2) + square(3.1416); // okay; the compiler can infer the type in the square() function and will do so as needed
}

//function templates pre C++20
template<typename T>
T square(T n)
{
return n*n;
}
int main()
{
return square(2) + square(3.1416); // also okay for the same reasons as above
}
```

**Containers: vector
- `std::vector` is in fact a class template, so the compiler can build a version optimized for each class
```
vector<int> v = {1,2,3}; //type can be specified
vector w = {1,2,3}; // or deduced (this is a vector<int>)
//loops
for(auto i:v) //modern syntax for for loops
{
std::cout << i << ''; // prints 1 2 3
}
for(int i=0; i<v.size(); i++); //older for loop syntax
{
std::cout << i << ''; // prints 1 2 3
}
```

HOMEWORK
>Print out the first 8 rows of Pascal's triangle.  This assignment is most easily completed by using a nested container

```
#include<iostream>

#include<vector>

#define FMT_HEADER_ONLY

#include<fmt/format.h>

#include<string>

using namespace std;

using namespace fmt;

int main() {

    const int num_rows=8;

    vector<vector<int>> triangle;

    for (int i=0; i<num_rows; i++) {

        vector<int> row(i+1,1);

        for (int j=1;j<i;j++) {

            row[j]=triangle[i-1][j-1]+triangle[i-1][j];

        }

        triangle.push_back(row);

    }

    int max_width=triangle[num_rows-1][num_rows/2];

    int field_width=to_string(max_width).length()+1;

    //print Pascal's triangle

    for (int i=0; i<num_rows; i++) {

        //print leading spaces

        cout<<string((num_rows-i-1)*field_width/2,' ');

        //print the actual values in the triangle

        for (int val: triangle[i]) {

            cout<<format("{:>{}}",val,field_width);

        }

        cout<<endl;

    }

    return 0;

}
```

>  Write a valid C program that is not a valid  
C++ program

```
#include <stdio.h>

//My approach to this problem was to utilize a keyword that is reserved in C++

//but was not reserved in C.

//"class" is the keyword I have used for this example. When this program declares the variable "class" on line 9

//this causes a C++ compiler to error, whereas a C compiler will proceed as expected.

int main() {

    char class[50];

    printf("What programming language are you learning in class? ");

    scanf("%s",class);

    printf("Good luck learning %s!\n",class);

    return 0;

}
```