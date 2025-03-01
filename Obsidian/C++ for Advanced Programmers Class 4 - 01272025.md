- automatic duration - duration tied to function scope
- can there be multiple f(i's at once)
	- what if it's incurred recursively?
		- different reference to the same f(i)
- automatic duration - lifetime is tied to the lifetime of the object it's in

dynamic duration - object tied to the lifetime of ownership of the object
- utilize std::unique_ptr
- we get to control how long the object exists, when it is created and when it is created
	- std::unique_ptr is a lightweight abstraction to do this
static duration - object created on program start, destroyed on program end

*Algorithms*
- there are some standard library ones
	- transform
	- accumulate
	- inner_product
- C++ algorithms are designed to interact with lambdas
	- There are some issues with the model of C++ algorithms
		- lack of compositionality
			- how can this be addressed?


Callables
- functors - objects that have an operator() parent
- sufficiently abstract, a class is technically callable
	- called via a member to get the value
- double f(int i) { return 3.2 * i; }
- can also call lambdas
	- if your lambda does not contain return statments taht return different types, the compiler is usually able to infer the return type
		- you are of course able to manually specify the return type

Capture lists
- to capture local variables by reference, use [&]
- to capture local variables by value, use [=]
- C++ makes const copies, so they may be hard to modified
	- can be fixed with the keyword mutable

All standard library algorithms can be used as easy for loops

While lambdas act as lot like functions, they may not necessarily be function
- do not just rely on their arguments, they also rely on what is capture
- under the hood, lambdas are implemented as classes that overload operator()

Polymorphic lambdas
- lambdas are able to take auto parameters and utilize type inference on the
	- lambdas are a class with an overloaded operator()

A lot more things than functions can be called
- function
- lambda
- functor

More C++ algorithms
- all_of
- any_of
- none_of
	- check if items in container/range have a specific property
- minmax, minmax_element
	- gets both the biggest and smallest items in the range
- is_heap, is_heap_until, is_sorted, is_sorted_until, partial_copy
	- ranged versions of move

many standard library algorithms can be run in parallel
- pass them a parallel execution policy
	- sort(execution::par_unseq, v.begin(), v.end());
		- parallel sort that can take advantage of GPUs

C++ algorithms are powerful with some issues
- they're getting increasignly problematic as functional programming gets more powerful
- they can't be composed
	- you can't transform then accumulate the same vector
		- need an intermediate vector
	- cumbersome notation
		- sort(v.begin(), v.end()); instead of sort(v);
	- ranges are the work in progress replacement

Best Practices on special member function
- even if oyu don't write them, the compiler automatically generates a:
	- copy constructor
	- copy assignment constructor
	- destructor
		- they're automatically generated, but they often aren't what you want
			- if you need a deep copy, you're SOL

base classes should have virtual destructors
- it might get destroyed by a unique_ptr to the base type4

Templates
- several ways to write code that apply same behavior across different types
	- object-orientation and templates
- template and generics are used more or less interchangably

a function template is not a function, it is a template to allow the compiler to create functions

Concepts
- concepts play the analogous role for generic programming that classes do in OO programming
- allows you to specify must-satisfy conditions for the template

"Generic programming should just be normal programming"

to learn about class templates, we'll create a "dense" matrix library
- if you have a class template, there are no arguments
	- need to put them in a template header
- can use a concept

A matrix is a 2D array of numbers
- natural initializer

The secret sauce for C++ templates is that if the generic definition fo the template isn't what you want, you can override it for the specific case
- compile-time analog to overriding a general method in a base class in a mroe specialized derived class


the determinant of a matrix is a numer that represents how much a matrix transformation expands it input
- formula that can be used to calculate
- Laplace expansion
A function, class or member can be fully specialized
- see definition of Matrix<1,1>::determinant() in Matrix.h

HW
modify Matrix.h to let you add matrices
- use operator overloading to make + and += work
to add 2 matrices, they must have the same shape
add them up

Modify each program to have a direct implementation of detrminants of 2x2 matrices
Test execution times

4-3 digs into the challenges of passing arguments
here we have some code