
# C++ Algorithms #
- there are some standard library ones
	- `std::transform`
	- `std::accumulate`
	- `std::inner_product`
## C++ algorithms are designed to interact with lambdas ##
- There are some issues with the model of C++ algorithms
	- lack of compositionality
		- how can this be addressed?
## Callables ##
- it is often good to pass things that can be called
	- what can be called?
		- functions - `double f(int i) { return 3.2 * i; }`
		- functors - objects that have an operator() parent
			- can be called exactly like functions
		- sufficiently abstract, a class is technically callable
			- called via a member to get the value
		- can also call lambdas
	- if your lambda does not contain return statements that return different types, the compiler is usually able to infer the return type
```
[](int x, int y) {  
int z = x + y; return z + x;  
}
```

- you are of course able to manually specify the return type as needed as well:
```
[](int x, int y) -> int {  
int z = x + y; return z + x;  
}
```

## Capture lists ##
- to capture local variables by reference, use `[&]`
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
- C++ makes const copies, so they may be hard to modify
	- can be fixed with the keyword `mutable`
```
int i{3};  
auto lambda = [=]() mutable { cout << i++; }; // lambda has a copy of i  
// Note: Only modifies lambda’s i, not the original variable
```

- All standard library algorithms can be used as easy for loops:
```
std::vector<int> someVec;  
int total = 0;  
std::for_each  
(someVec.begin(), someVec.end(),  
[&](int x) { total += x; });
```

- While lambdas act as lot like functions, they may not necessarily be functions
```
int i = 7;  
auto x = [&i](int j) { return i*j; }  
/* ... */  
cout << x(5);
```
- in this example, `x` isn't really a function because it doesn't just depend on its argument `j`, it also depends on the captured reference to `i` (`&i`)
- under the hood, lambdas are implemented as classes that overload operator()
	- the above example has a type that looks like this:
```
struct X {  
X(int &i) : i(i) {} // Captures i  
int operator()(int j) {  
return i*j;  
}  
int &i;  
};
```

## Polymorphic lambdas ##
- lambdas are able to take auto parameters and utilize type inference on them
	- `[](auto x) { return x*x; }(7); // 49`
	- `[](auto x) { return x*x; }(7.5); // 56.25`
	- this works because lambdas are a class with an overloaded operator()

- Printing the even numbers of a vector...
	- with a for loop:
```
vector v = {2, 5, 6, 9};  
size_t evenCount = 0;  
for(int i: v)  
if(i % 2 == 0)  
evenCount++;  
cout << evenCount;
```
- with an algorithm:
```
vector v = {2, 5, 6, 9};  
cout << v.count_if([](int i) { return i%2 == 0; });
```

- Captures can also be helpful as well:
	- to use a for loop to draw a hand of cards for each player:
```
CardDeck d;  
vector<Person> players;  
for(auto p: players) {  
p.hand = d.draw();  
}
```
- utilizing an algorithm:
```
CardDeck d;  
vector<Person> players;  
for_each(players, [&](auto p) {p.hand = d.draw(); }
```

## Functors ##
- if you write your own class with an overloaded `operator()`, it's called a `functor`
	- allows you to create stateful objects that can be called like functions
```
struct Accumulator {  
int()(int j) { i = i + j; return i }  
int i = 0;  
};  
Accumulator acc;  
cout << acc(1); // Prints 1  
cout << acc(2); // Prints 3 = 1+2  
cout << acc(4); // Prints 7 = 1+2+4
```

- A lot more things than functions can be called:
	- function
	- lambda
	- functor

## More C++ algorithms ##
- `all_of`
- `any_of`
- `none_of`
	- check if items in container/range have a specific property
	
```
copy_n  
vector<int> v = getData();  
// Print 5 elements  
copy_n  
(v.begin(), 5,  
ostream_iterator<int>  
(cout, "\n"));
```

```
find_if_not  
vector<int> v = { 1, 3, 5, 6, 7};  
// Print first elt that is not odd  
cout << *find_if_not  
(v.begin(),  
v.end(),  
[](int i) {  
return i%2 == 1;  
});
```

```
partition_copy  
vector<int> primes;  
vector<int> composites;  
vector<int> data = getData();  
extern bool is_prime(int i);  
partition_copy  
(data.begin(),  
data.end(),  
back_inserter(primes),  
back_inserter(composites),  
is_prime);
```

- `minmax`, `minmax_element`
	- gets both the biggest and smallest items in the range
- `is_heap`, `is_heap_until`, `is_sorted`, `is_sorted_until`, `partial_copy`
	- ranged versions of `move`

## Many standard library algorithms can be run in parallel ##
- pass them a parallel execution policy
	- `sort(execution::par_unseq, v.begin(), v.end());`
		- parallel sort that can take advantage of GPUs

## C++ algorithms are powerful with some issues ##
- they're getting increasingly problematic as functional programming gets more popular
	- they can't be composed
		- you can't transform then accumulate the same vector
			- need an intermediate vector
		- cumbersome notation
			- `sort(v.begin(), v.end()); instead of sort(v);`
	- ranges are the work in progress replacement
		- composable
		- work directly on ranges rather than iterator pairs
		- lazy for efficiency (don't have to create intermediate containers)
		- fully conceptized

# Best Practices on special member functions #
- even if you don't write them, the compiler automatically generates a:
	- copy constructor
```
struct X { X(X const &); /* ... */ };  
X x2{x1};
```

- copy assignment constructor
```
struct X { X &operator=(X const &); /* ... */ };  
x2 = x1;
```

- destructor
```
struct X { ~X(); /* ... */ };
```

## Known as "The Rule of Three" ##
- although these are generated automatically by the compiler, the built-in ones often aren't what you want
	- typically when you need special behavior like a deep copy
		- usually need a deep copy assignment
			- and a corresponding deep destruction
- in general, if you define one, you should consider whether you have to define all three

## Rule of the Big Two ##
- you generally don't need to work about the destructor if the member destructors clean everything up
	- will be generalized with the RAII principle


## Base classes should have virtual destructors ##
- if the class is designed to be inherited from, the destructor must be virtual
	- if you don't, classes that inherit from your base class won't have destructors
```
struct Animal {  
/* ... */  
virtual ~Animal() = default;  
};
```

# Templates #
- there are several ways to write code that apply same behavior across different types
	- object-orientation and templates
- template and generics are used more or less interchangably
Template example:
```
auto square(auto x) {  
return x*x;  
}  
int i = square(21); // 441  
double d = square(2.1); // 4.41
```

a function template is not exactly a function, it is a template to allow the compiler to create functions

Object Oriented Programming and templates can solve similar problems:

## Concepts ##
- concepts play the analogous role for generic programming that classes do in OO programming
- allows you to specify must-satisfy conditions for the template
```
template<typename T>  
concept Animal = requires(T a) {  
{ a.eats() } -> convertible_to<string>;  
{ a.name() } -> convertible_to<string>;  
};
```

"Generic programming should just be normal programming"

## Kinds of templates ##
- function templates
	- `std::sort`
- Class templates
	- `std::vector`
- Variable templates
- To learn about class templates, we'll create a "dense" matrix library
	- if you have a class template, there are no arguments
		- need to put them in a template header
	- can use a concept
## Sometimes you need a more verbose notation ##
- for function templates, we could simply use "concept auto" arguments
- if you have a class template, there are no "arguments", so you put them up front in a "template" header
	- useful if you need a name for the type
- can also use a concept
	- `template<floating_point T> struct complex;`
	- pre C++20 version:
```
template<typename T> // Simple vector def  
struct vector { /* ... */ };
```

# Initializer lists #
- one of the issues with vectors used to be that it was difficult to initialize them
```
/ Yuck!  
int init[] = { 0, 1, 1, 2, 3, 5};  
vector<int> v(init,  
init+sizeof(init)/sizeof(init[0]);
```

- as of C++11, you can initialize vectors as easily as C arrays:
```
vector<int> v = {0, 1, 1, 2, 3, 5};
vector v = { 1, 2, 3, 4};
```
- this works because `std::vector` has a constructor that takes a `std::intiializer_list<T>`
	- represents a "braced initializer of Ts" expression
	- initializer lists have `begin()`, `end()` and `size()` methods so your constructor can iterate through their value

The secret sauce for C++ templates is that if the generic definition of the template isn't what you want, you can override it for the specific case
- compile-time analog to overriding a general method in a base class in a more specialized derived class

# Matrix determinants #
the determinant of a matrix is a number that represents how much a matrix transformation expands it input
- Laplace expansion
- we can just use a formula for smaller matrices

A function, class or member can be fully specialized
- see definition of `Matrix<1,1>::determinant()` in `Matrix.h`
	- this definition is only designed for and only used for 1x1 matrices

function templates can be overloaded

## Partial specialization ##
- only classes may be partially specialized
- Template class:
```
template<class T, class U>  
class Foo { ... };
```
- Partial specialization:
```
template<class T>  
class Foo<T, int> {...};
```
- the partially specialized class has not particular relation to the general template class
	- need to either redefine bad common functionality or inherit good functionality