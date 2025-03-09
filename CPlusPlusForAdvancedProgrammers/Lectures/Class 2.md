
`using namespace std;` //don't do this in a job interview

`typedef vector<int> Row;` //tired, old fashioned; assigns `vector<int>` to Row
`using Triangle=vector<Row>;` //alternative notation
- the above 2 are a useful abstraction to  make it easier to change types later; also makes it easier to understand

- `typedef` notation is dated, should be avoided; main problem is it can lead to ambiguous parsing
- cannot have a template typedef, can only use `using`

>"Almost everything written by good c++ programmers is templatized"


for loop notation - `for (element:element)` where the second element is an iterable
- can use auto for the first element to infer the type

don't use `int const var = value`, use `int constexpr var = value`
- since it doesn't represent a compile-time determined value, it doesn't really make sense
	- compiler cannot count on constant being a constant when using `int const`
		- can with `constexpr`
			- can only use `constexpr`s as template arguments

when you do division on two things of the same type, the results will be of the same type

`ostringstream`
 - sends data stream result to a string
```
ostringstream os;
os<< i;
return os.str().size(); // will get an error that it returns an unsigned type - fine as long as it's an int
//most modern languages dont' have signed/unsigned types
```

to get the position of an iterator, and not the item of it, de-reference it with an asterisk, i,.e, `*max_element`

# LECTURE #

- a program refers to data objects through expressions in C++ (or whatever language is being used)
- when the program is run, actual data objects exist in computer memory
- how does the code find, interpret, and manage objects at runtime?
- when the program creates a variable at runtime, it allocates memory on the stack to hold the data
	- any expression referencing the variable will use the data there
![[Pasted image 20250305194214.png]]

- in C++, when we do an assignment, the object is copied

```
int i =3;
int j=i;
i=5;
cout<<j; //prints 3
```

- both `i` and `j` have their own storage associated with them in the running program
![[Pasted image 20250305203437.png]]

If we wanted `j` to refer to the same memory in `i` as opposed to a copy of the same data, we can do that by creating `j` as a reference to an existing object (`int &`)


```
int i-3;
int &j=i;
i=5;
cout<<j; //prints 5
```

- the big difference between references and values is that values have their own copy of the data, and the reference reuses the object it is initialized with without making a copy
	- both `i` and `j` can be used the same way to refer to the same way after creation
- WARNING: object lifetime: if multiple variables refer to the same object, you have to be careful, that the object still exists when you use it

```
int &f() //f is a function returning a reference to an int
{ int i=; return i;}

int &j = f();    //f returns a reference to f's i
				 //so the int&j also uses that as it's storage
				//when f is done running, the storage for i is released
				// so j is bound to a no-longer existing object
cout << j; //undefined behavior!

```

## C++ programmers must manage the lifetime of objects explicitly ##
- not a garbage collected language like Java
- if memory is released too early, the program can crash from attempting to use an object that no longer exists
- if memory is not released when it is no longer needed, the program can run out of memory (memory leak)

## C++ has built-in commands to dynamically create and destroy objects ##
 - `new` and `delete`
	 - should be rarely used
- should utilize a scoped ownership model
	- most import idiom in C++: RAII

## "Automatic manual memory management" ##
- most common abstraction - `unique_ptr`
- `make_unique<T>` creates an object in memory on demand and returns a handle to the object of type `unique_ptr<T>` that can be used to reference the object
```
//Create an int in memory and return an associated unique_ptr
unique_ptr<int> ui = make_unique<int> (5);
```

## `unique_ptrs` give you access to the data in the object ##
- when you need to reference the object managed by the unique_ptr, use the `*` operator
```
unique_ptr<int> ui = make_unique<int>(5);
cout << *ui; //Prints 5
```
- the `unique_ptr` manages the lifetime of the object
	- when the `unique_ptr` goes away, the memory of the object it is managing is automatically freed up
- if you point an existing `unique_ptr` to a new object, the old object will be destroyed before the new one is created and managed by the `unique_ptr`
```
auto ui = make_unique<int>(5);
cout << *ui; //Prints 5
ui = make_unique<int>(2);
cout >> *ui; //Prints 2
```
- the `unique_ptr` automatically releases the memory of the first object (with value 5) and then starts managing the new object (with value 2)

## Transferring ownership ##
- can't simply assign
	- `up2=up1; // Doesn't work, two "unique" owners"`
- correct solution: move from `up1`
	- `up2 = move(up1); // up2 is now the owner, don't use up1`
- with the advent of `unique_ptr` avoid manually managing memory
	- `new`, `delete`, `malloc`, and `free`
		- covered later in the course
		- should only be used in systems programming that requires low-level manipulation

# CLASSES #
- C++ would be a very limited language if we couldn't define our own types
- To create your own type in C++, you must define a class:
```
struct Student_Info {
string name;
double midterm, final;
vector<double> homework;
}; //required
```
- difference from C:
	- no need to add `typedef struct Student_info Student_info;`
- the only difference between a `struct` and a `class` is different default visibility of members. This is the equivalent class:
```
class Student_info {
public:
	string name;
	double midterm, final;
	vector<double> homework;
};
```

- `public` members of a class are fully visible to all
- `protected` members are visible only to subclasses
- `private` members are only visible within the class
```
class A {  
void f() {  
cout << pub; // OK  
cout << prot; // OK  
cout << priv; // OK  
}  
public:  
int pub;  
protected:  
int prot;  
private:  
int priv;  
};  
class B : public A {  
void g() {  
cout << pub; // OK  
cout << prot; // OK  
cout << priv; // Error  
}  
};  
void h(A a)  
{  
cout << a.pub; // OK  
cout << a.prot; // Error  
cout << a.priv; // Error  
}
```

- a class can have methods as well as fields
```
struct Student_Info { // In header
string name;
double midterm, final;
vector<double> homework;

//Method to calculate the student's grade
double grade() const {
return (midterm+final+median(homework))/3;
}
};
```

- you can also put methods in separate files, i.e. declare class definition in .h file, declare the method in the .cpp file
	- you have to `#include` the header file in the other one (leverage the pre-processor)
```
// In .h file  
struct Student_info {  
string name;  
double midterm, final;  
vector<double> homework;  
double grade() const;  
};  
// In .cpp file  
double Student_info::grade() const  
{  
return (midterm + final + median(homework))/3;  
}
```

- to access methods and fields of a class, use the `.` operator
```
Student_info s;  
s.name = "Mike";  
s.midterm = 70;  
s.final = 85;  
s.homework.push_back(60);  
s.homework.push_back(75);  
cout << s.grade();
```
- a program uses expressions to refer to objects in memory
	- the static type is the type of expression
		- known at compile time
	- the dynamic type is the type of the actual object referred to by the expression
		- may only be knowable at run-time
	- static and dynamic types generally only differ due to inheritance

## Single inheritance ##
- inheritance can be used to model an "isA" relationship
```
struct Animal { /* ... */ };
class Gorilla: public Animal {...};
```

- in this example, Gorilla "isA" `Animal` and can be referred to by `Animal` references
- Static type vs. Dynamic type
```
int i = 5; // S = int, D = int  
Gorilla g; // S = Gorilla, D = Gorilla  
Animal &a = g; // S = An&, D = Gor  
Animal a2 = g; // Oops! Can’t copy a Gorilla  
// into an Animal  
unique_ptr<Animal> ua = make_unique<Gorilla>();  
// Static type of *ua is Animal but Dynamic is Gorilla  
ua = make_unique<Falcon>();  
// Now S = Animal, D = Falcon
```

## Virtual vs. non-virtual method ##
- a virtual method uses the dynamic type
- non-virtual method uses static type
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

- previously used `(*g).f()` to call the f method of the object managed by the `unique_ptr` g
	- `(*g)` is a reference to the `Gorilla` type object managed by g
	- `.f()` calls the method of the object
- this is very clunky, but has a special shortcut notation:
	- `g->f() // Identical to (*g).f()`

## Leveraging virtual methods to add multiple grading strategies ##
```
struct Abstract_student_info { // In header  
string name;  
double midterm, final;  
vector<double> homework;  
istream& read(istream&);  
// Don’t define grading strategy here; don't need to define, will be overriden by BalancedGrading and IgnoreHomework  
virtual double grade() const = 0;
};

struct BalancedGrading: public Abstract_student_info {  
virtual double grade() const override {  
return (midterm + final + median(homework))/3;  
}  
};  
struct IgnoreHomework: public Abstract_student_info {  
double grade() const {  
return (midterm + final)/2; // Ignore the HW  
}
// BalancedGrading and IgnoreHomework are only effective due to the virtual grade() method
};
```

## Virtual Method Performance ##
- a virtual function is called based on the runtime type of the object even if it is accessed through a base class pointer
	- you may have heard virtual functions have one more indirection
		- you don't need to worry about performance
			- ...most of the time
- Since the compiler doesn't know what the type is at compile-time, a virtual function is called through a reference to a table of functions stored in the object
	- in comparison, a non-virtual method is known to the compiler and directly called by the compiler
	- this generally only adds a couple of clock cycles, so the performance cost is usually negligible
	- the main cost is the loss of inlining and function level optimization
		- the more virtual functions that are used, the less the compiler is able to understand your code to effectively optimize it
- Best practices
	- it is typically fine to make methods virtual
		- performance cost is "amost always" minimal, and the effect if powerful in object-oriented code
	- don't use virtual functions gratuitiously if you are not using object-orientation
		- especially important in performance critical or low level code
			- virtual functions can impeded compiler code optimization and modify memory layout in unpredictable ways


the first rule of optimization is:
DON'T
- highly-optimized code is generally harder to read and maintain
- there is no point in optimizing something that is not a bottleneck
	- benchmark to find the bottlenecks, then decide whether they're worth optimizing

## C++ has the `<chrono>` library to work with time ##
- can use `system_clock::now()` to get start and end times
	- may not always be a good option

## "as-if" rule ##
 - as long as it behaves as if it did things the right way, the compiler can reorder code however it wants
	 - this means that if you start your code with a `system_clock::now()` and then calculate the difference at the end of your code, the compiler may move your `system_clock::now()`s right next to each other

- be cautious about timestamps, use proper benchmarking tool
	 - Google Benchmark
	 - Nonius
	 - gprof

## Order of construction ##
1. Virtual base classes first
2. Base class constructors
	1. run in the order they are declared
3. Member constructors
	1. run in the order of declaration
- very subtle and easy to get wrong
	- **Best Practice** don't relay on it, don't use virtual functions in constructors


## Avoiding spaghetti inheritance ##
- number of classes can grow exponentially
- better to create one class that can covers as many edge cases as possible if possible




