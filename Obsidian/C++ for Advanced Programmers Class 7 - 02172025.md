how do we find the largest value in a list and the location of it? use an iterator

when you utilize RAII, your code automatically becomes exception safe

`operator new` is similar to malloc in C
- allocates requested number of bytes in memory
- `operator new(10)` allocates 10 bytes of memory and returns a `void *` pointing to it
- if it fails (typically because there is not enough memory), it throws a `std::bad_alloc` exception
	- can also be because a bad value was passed to operator new/malloc
- You can use operator overload on operator new
	- `void operator new(size_t s, void *p` {return p;}
		- `new(p) A` returns a pointer to the memory location
			- lets you put memory in a specific place

- sometimes, when allocation fails, you want to return nullptr instead
- using new returns a raw pointer
	- this violates exception safety using RAII
	- should be avoided if `std::unique_ptr` can perform the function
`make_shared` and `make_unique` create an object and return an owning pointer
	preferred when possible

sometimes, when you have a smart pointer, you need the raw pointer instead
- can use `f(a.get()`, which gives you the raw `a*`

- `delete` takes a raw pointer to an ojbect, calls its destructor, then releases its memory
	- much better to use smart pointers and RAII instead

Atomics
- avoiding locks
	- when you lock, the protection section of code cannot be parallelized
	- atomics let you write multithreaded programs without locks

- C++ has a library of atomic types can be shared between threads
- atomic counter
	- can read with with `read()`, write with `store()` and increment/decrement with ++/--

Sleeping a thread
- sometimes you want to let a thread to sleep for a period of time and then resume work
	- robust, stronger typing  can help you avoid errors relating to 

`std::chrono`
- clocks, source of time information
	-  now - `std::chrono::system_clock::now()`
	- duration - length of time
		- to use, cast to the type you want, then use count
			- `std::chrome::seconds(std::chrono::days(1)).count()`
			- sadly, the notation sucks
	- points in time associated with a clock
		- add and subtract time with each other
	- better defined litearls make it better
		- `40ms` is 40 milliseconds
String literals
- a C string literal is actually of type `char const *`
	- can do wrong with conversion to C++ strings
	- add s on the end, i.e. `"hello, world"s`

Inter-thread communication

futures and promises
- paired, let you pass values between threads
	- kind of like a producer/consumer but you are just passing a single item
- a promise lets you pass a value from one thread to another
- create a paired future and promise
	- `promise<int> p;`
	- `future<int> fi = p.get_future()`
- 