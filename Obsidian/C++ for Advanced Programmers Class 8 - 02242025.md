`decltype` - gives you the type of an expression without calling the expression

handlign errors feels like the biggest part of turning a POC into production code
- C uses error codes to detect errors
- C++ relies on exceptions, which are impossible to ignore

exceptions can cause clean up code to be missed
- it's possible to fix this with RAII

Best practice: don't throw exceptions in your destructor
- it it may, be sure to catch before returning

Exception-safe interfaces
- when you write a function or method, you should think about what happens if an exception occurs while passing arguments

if there is any chance a move throws, then you have to copy

`noexcept`
- a function or method can be labelled as `noexcept`, which means it will never throw an exception
	- `std::terminate` will be called if it tries

if a type has a `noexcept` move constructor, then it moves
- otherwise, it copes it
- `unique_ptrs` move constructor is annotated as `noexcept`
- there is a type trait called `is_nothrow_move_constructible_v` that can be used with SFINAE or  Concepts to call the right code

C++ used to have exception specifications
- removed in C++17

there is a function named `noexcept` that returns True if it is `noexcept`

destructors should almost always be `noexcept`
- class destructors are implictly `noexcept`
- you have to explicitly say `noexcept(false)` if you want a destructor to be able to throw an exception

if classes are `noexcept`, make usre to declare them that way
- don't try to declare everything with the correct `noexcept` specifier
	- don't hesitate if there's a benefit to do it though

- while exceptions are the norm, they're complicated
	- many systems disable exceptions
		- this breaks much of the standard library
	- C++23 added a new way to handle errors: `std::expected`

Polymorphic containers
- so far, all containers we have used only contain objects of one types
	- `vector<int>` holds vectors
- we can use inheritance and virtual functions to put objects of different dynamic types in a container
	- you may want different static types

`pair` and `tuple`
- are to classes what lambdas are to functions

`tuple` 
- anonymous struct with fields of given types
	- can user `get<>` to access the fields

one natural use for pair and tuple is to let functions return multiple values
`pair<int,int> F(){ return {1,2};} // OK`

pairs are like tuples that always have two types
- `pair<int,string>={1,"foo"};`
- only difference is members have a name
	- p.first
	- p.second

there aren't many reasons to use pairs now that tuples exist
- simply use a tuple of size 2

Structured Bindings
- work very well with functions that return multiple values in a pair or tuple
	- want to get several values back, but want them after you've called the function
		- `std::tie` is the old way

`std::optional`
- sometimes it's useful for an object to be unset
	- like None in python
	- pointers can be `null`, but don't want to use if we don't have to

C++ has a lightweight "variant" abstraction that generalizes unions
- `variant<A,B>` holds either an A or a B


Concepts play the analogous role for generic programming that base classes do in OOP
- explain what operations a type supports