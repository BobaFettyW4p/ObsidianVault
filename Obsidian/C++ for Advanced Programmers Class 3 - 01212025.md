1. Pre-Lecture

- Special Members of Classes
	- previous seen:
		- putting ordinary data members and methods in a class
	- Static Class Members
		- usually a class member requires you to know what object it applies to
	- Sometimes you want the same class member to be shared by all objects of a class
		- you may need to initialize static data members with class types in a separate .cpp file
			- this is to ensure memory is allocated only once, not every time the header is included
	- Constructors and Destructors
		- you invoke a constructor to create an object
- Constructors for fundamental types
	- since they dont' have them by default, creating one without an intiializer gives you garbage values
		- int i; //could be anything
		- reading uninitialized memory is a common source of bugs
		- if you explicitly pass it zer constructor arguments, the memory is zero-initialized
			- int i(); // i is 0
- special constructors
	- some make it easier to create objects
		- C++ will automatically create them
			- default constructor
			- copy constructor
			- aggregate constructor
			- move constructor - will be covered later
- default constructors:
	- if you don't declare any constructors, is it impossible to construct an object of the type?
		- No, it breaks compatibility with C
		- if you don't declare a constructor, C++ will create a no-argument constructor for you
			- it may not be optimal (uninitialized memory)
			- you can simply create your own constructor and not rely on the default one
- Non-Static Data Member Initializers (NSDMI)
	- you can provide default initializers for members and the default constructor will construct with those values
		- static data members cannot be initialized in class
you are also able to construct a new object by copying an existing one
S s;
S s2 = s;
- this is legal C
- what constructor does it call?
	- the copy constructor
Copy constructor
- the compiler implicitly generates a copy constructor that copies all of the base classes and members
What if the compiler generates the wrong copy constructor?
- in a binary tree, it only copes the root
	- tangled trees
		- you have to write your own copy constructor

"Singleton Pattern"
- represents a class that only ever has one object
- in this case you would not want a copy constructor at all
- need to delete the copy constructor

struc Logger {
Logger (Logger const &) = delete;
static Logger myLogger;
/* ... */
};

Aggregate construction
S s= {5,6.7}
- this is valid C
Aggregate construction
- if a class ia simple data structure with public members, C++ considers it to be an aggregate class and generates a constructor that takes an initializer for every field

Class destructors
- you can write them
struct A {
~A() {
/*Code to cleanup resources used by A*/
}
}

if you don't write one, the compiler will generate one for you
- the default destructor
	- simply destroys all fields and base classes in the order they were created

Unique_ptr
- class that managers an object in memory
	-  it can give you a reference to an objet
	- the object it manages is always cleaned up when it goes away
- unique_ptr has a specially written destructor that destroys the object and releases the memory
	-  memory management is ~40% of C development, but much less in C++
- special name - RAII
