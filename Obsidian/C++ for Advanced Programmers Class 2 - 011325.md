
Pascals Triangle
libraries
- vector
- numeric
- iostream
- string
- iomanip
- algorithm
- sstream

using namespace std; //don't do this in a job interview

typedef vector<int> Row; //tired, old fashioned; assigns vector<int> to Row
using Triangle=vector<Row>; //alternative notation

the above 2 are a useful abstraction to  make it easier to change types later; also makes it easier to understand

typedef notation is dated, should be avoided; main problem is it can lead to ambiguous parsing
- cannot have a template typedef, can only use `using`

Almost everything written by good c++ programmers is templatized

for loop notation - for (element:element) where the second element is an iterable
- can use auto for the first element to infer the type

don't use int const var = value, use `int constexpr var = value`
- since it doesn't represent a compile-time determined value, it doesn't really make sense
	- compiler cannot count on constant being a constant when using int const
		- can with constexpre
			- can only use constexprs as template arguments

when you do division on two things of the same type, the results will be of the same type

ostringstream
 - sends data stream result to a string
ostringstream os;
os<< i;
return os.str().size(); // will get an error that it returns an unsigned type - fine as long as it's an int
	- most languages don't have signed/unsigned types anymore

to get the position of an iterator, and not the item of it, de-reference it with an asterisk, i,.e, *max_element

LECTURE

a program refers to data objects through expressions in C++ (or whatever language is being used)

when the program is run, actual data objects exist in computer memory

how does the code find, interpret, and manage objects at runtime?

in c++, when we do an assignmentm, object is copie

```
int i =3;
int j=i;
i=5;
cout<<j; //prints 3
```

to store the same data, you can reference the same memory

```
int i-3;
int &j=i;
i=5;
cout<<j; //prints 5
```

object lifetime: if multiple varialbes refer to the same object, you have to be careful, that the objet still exists when you use it

```
( int i=3; return i; )

int &j=f();

cout<<jl // undefined behavior because the function ceases to exist as soon as it stops running

```

C++ programmers must manage the lifetime of objects explicitly
- not a garbage collected language like Java
- if memory is released too early, the program can crash from use an object that no longer exists
- if memory is not released when it is no longer needed, the program can run out of memory (memory leak)

C++ has built-in commands to dynamically create and destory objects
 - `new` and `delete`
	 - should be rarely used
- should utilize a scoped ownership model
	- most import idiom in C++: RAII

"Automatic manual memory management"
- most common abstraction - `unique_ptr`

`make_unique<T>` creates an object in memory on demand and returns a handle to the object of type `unique_ptr<T>` that can be used to reference the object

unique_ptrs give you access to the data in the object, use the * operator
```
unique_ptr<int> ui=make_unique<int>(5);
cout<<*ui; // prints 5

unique_ptrs manage the lifetime of the object
- when the unique_ptr goes away, it will automatically free up the memory of the object

if you point an existing unique_ptr to a new object, the old object will be destroyed

- can't simply assign `up2`=`up1`; // Two "unique" owners
- use move
	- `up2=move(up1)`


avoid new, delete, malloc, and free
- covered later in the course
- should only be used in systems programming that needs low-level manipulation


to create your own type in C++, you must define a class

```
struct Student_Info {
string name;
double midterm, final;
vector<double> homework;
}; //required
```
see sample "Grading" programs on Canvas
unlike C, no need for `typedef struct Student_info student_info`

the only difference between a struct and a class if the default visibility of members

```
class Student_info {
public:
	string name;
	double midterm, final;
	vector<double> homework;
};

public members are bisible to all, protected members are visible only to subclasses, private members are only visible within the class

a class can have methods as well as fields

struct Student_info { 
//method to calculate student's grade

double grade() const { //const means this method does not change pre-existing methods
return (midterm+final+median(homework))/3;
}
};

you can also put methods in separate files, i.e. declare class definition in .h file, declare the method in the .cpp file
- you have to #include the header file

a program uses expressions to refer to objects in memory
- the static type is the type of expression
	- known at compile time
- the dynamic type is the type of the actual object referred to by the expression
	- only knowable at run-time
- static and dynamic types generally only differ due to inheritance

virtual vs. non-virtual method
- virtual uses dynamic type, non-virtual uses static type

(*g).f() to call the f method of an object managed by unique_ptr g
	- can just use notation g->f();


a virtual is called based on the runtime type of the object even if it is accessed through a base class pointer
	- you may have heard virtual functions have one more indirection
		- you don't need to worry about performance
			- ...most of the time

apostrophes are ignored in numbers, convenient to mark digits


the first rule of optimization is don't
	- there is no point in optimizing something that is not a bottleneck
	- benchmark to find the bottlenecks, then decide whether they're worth optimizing

C++ has the <chrono> library to work with time

"as-if" rule
 - as long as it behaves as if it did things the right way, the compiler can reorder code however it wants

be cautious about timestamps, use proper benchmarking tool
 - google benchmark

order of construction
1. base class
2. base class constructors
3. member constructors in order of declaration
- very subtle and easy to get wrong


avoiding spaghetti inheritance
- number of classes can grow exponentially
- better to create one class that can covers as many edge cases as possible if possible


HW2.1.1
in <algorithm>, std::transform, that paplys a function each element; put several doubles in vector, use std::transform to produce a new vector with each element equal to the square
HW2.1.2
function in <numeric> called accumulate that can add all items in a collection, use accumlate to add all squares and take the square root
HW2.1.3
use std::inner_product to find a better way to complete part 2
HW2.1.4EC
use 4 argument version of accumulate to accomplish it in a single step




