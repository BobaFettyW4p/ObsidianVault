- how do we find the largest value in a list and the location of it? use an iterator

- when you utilize RAII, your code automatically becomes exception safe

## `operator new` ##
- similar to `malloc` in C in that it allocates the requested number of bytes of memory
	- `operator new(10)` allocates 10 bytes of memory and returns a `void *` pointing to it
	- if it fails, it throws a `std::bad_alloc` exception
### Can you use operator overloading? ###
- Yes!
	- you can use C++ operator overloading to define your own `operator new`s
		- the standard library defines a couple of useful overloads

### Placement `new` ###
- suppose you already have memory and want to put an object in it
	- you don't want to use `new A`
		- the object will be placed someplace else
			- the standard library provides an overload that you can tell where you want it to put the object
```
void operator new(size_t s, void *p) { return p; }
new(p) A; // The A object will be at location p
```

### `nothrow new` ###
- sometimes you don't want to `new` to throw an exception when allocation fails but return `nullptr` instead
	- this is similar to C
	- standard library provides a non throwing overload
		- `new(std::nothrow) A; //won't throw`
		- can be applied throughout the entire program, just overload regular `operator new` 

- the problem with `operator new` is that it returns an owning raw pointer
	- this violates exception safety by not using RAII:
```
void f()  
{  
// g(A *, A *) is responsible for deleting  
g(new A(), new A());  
}
```
- if `A` constructor excepts the second time it's called, the first `A` object will be leaked

## `make_shared<T>` and `make_unique<T>` ##
- they create and object and return an owning pointer
- these two lines act the same:
	- `auto ap = make_shared<T>(4,7);`
	- `shared_ptr<T> ap = new T(4,7);`
```
void f()  
{ auto a1 = make_unique<A>(), a2 = make_unique<A>();  
// g(A *, A *) is responsible for deleting  
g(a1.release(), a2.release());  
}
```
- if we can modify `g()`, we really should change it to take a `unique_ptr<T>`
	- otherwise we have an owning raw pointer
		- `g()` takes ownership, so it shouldn't use owning raw pointers
		- `g(make_unique<T>(), make_unique<T>());`
			- now we can call `g(make_unique<T>(), make_unique<T>());`
		- the following doesn't work because ownership will no longer be unique:
```
auto p1 = g(make_unique<T>();  
auto p2 = g(make_unique<T>();  
g(p1, p2); // Illegal! unique_ptr not copyable
```
- to fix, we need to move from `p1` and `p2`
	- `g(move(p1), move(p2)); // OK. unique_ptr is movable`
## Getting raw pointers from smart pointers ##
- sometimes, you have a smart pointer, but need a raw pointer instead
	- a function might need the address of an object, but does not want to participate in managing the object's lifetime
```
f(A *); // Doesn’t decide when to delete its argument  
auto a = make_unique<A>{};  
f(a.get()); // a.get() gives you the raw A *
```

- sometimes you want to extend the lifetime of an object beyond the lifetime of the `unique_ptr`
```
g(A *); // g will delete the argument when done  
auto a = make_unique<A>{};  
g(a.release()); // a no longer owns the object
```

## How to get rid of an object ##
- when you are done with an object, it needs to be destroyed and its memory released
	- `delete` takes a raw pointer to an object, calls the destructor, and then releases its memory
```
Animal *ap = new Elephant();  
ap->eat();  
delete ap; // Note: Missed if eat() throws an exception
```


# Atomics #
- Atomics are used to avoide locks
	- when you use lock, the protection section of code cannot be parallelized
	- atomics let you write multithreaded programs without locks
- sometimes you just want a variable you can read and update from variable threads
	- a lock is a little excessive for this
	- x86 has a built-in atomic increment, so why wouldn't a low-level programming language not be able to implement it?
	- C++ has a library of atomic types can be shared between threads
- atomic counter
	- can read with with `read()`, write with `store()` and increment/decrement with `++` or `--`

## How to quickly allocated objects of a fixed size? ##
- say we're allocating 32-byte objects from 4096-byte pages
	- divide each page in our memory pool into 128 objects in a linked list
		- next, allocate and deallocate 32-byte objects from the list by pushing and popping
			- fewer than a dozen instructions v. hundreds in a conventional allocator
				- make sure you lock for thread-safety
![[Pasted image 20250307071002.png]]

## Real-Life example ##
- a programmer released an application with the above linked list allocator
	- it seemed to speed up the application considerably
	- but customers reported the application became slow as the number of threads into the hundreds
		- even though the lock only protects a few instructions, if a thread holding the lock loses its quantum, the list is unavailable until that thread gets another timeslice
### Can we make a thread-safe list without locks? ###
- we "need" the lock because we need to both return A and update the head to point to B atomically
	- C++ has an atomic `compare_and_exchange_weak` primitive that does a swap, but only if the target location has the value that we would expect
		- our update will fail if another thread messed with the list in the critical section
			- if so, loop back and try again
![[Pasted image 20250307071248.png]]
- this doesn't quite work
	- some other thread could make multiple changes to get back to the same end state
		- i.e. if they push, then pop before our thread can act, leaving the head unchanged
![[Pasted image 20250307071347.png]]
- after `compare_and_exchange_weak` B is erroneously back on the list
![[Pasted image 20250307071423.png]]

- how can we fix this?
	- add a "list operation counter" to the head
		- every time an operation happens to the head, it increments
			- our atomic operations will only act if the state is the same, and the counter is the same
![[Pasted image 20250307071519.png]]
- why is this better?
	- no need for memory barrier
	- only one atomic operation instead of two
	- if the thread loses its quantum while doing the list operation, other threads are free to manipulate the list
		- this is the big one
	- this works on x86-32,x86-64 and Sparc

- what about `push`?
	- the same techniques work for pushing onto the list
		- the techniques aren't restricted to lists
			- many other lock-free data structures are known

### Back to the thread safe list ###
- switching to using Compare and Exchange-based atomic lists on Sparc improved performance
	- customers experienced extremely intermittent list corruption
		- impossible to debug
			- the root cause - Solaris interrupt handlers only saved the bottom 32-bits of some registers
				- Moral: The first rule of optimization is "Don't!"
			- later, the program started being used on SMP systems, and having performance issues
				- compare and exchange locked the bus to be thread-safe, but gets more and more expensive and the number of processors increased
					- they would eventually go back to a lock-based list
## Should you customize your memory allocation? ##
- do you want to pollute your class with deep assumptions about the hardware and operating system?
	- do you want to update your code everytime one of the two changes?
	- the answer is almost always "no"
		- products may not be usable without a custom memory manager
			- they're powerful tools that need to be used wisely
				- difference between "use" and "abuse"

## Sleeping a thread ##
- sometimes you want to let a thread to sleep for a period of time and then resume work
- traditional threading libraries have a sleep function, i.e. `sleep(30)`
	- how long is 30? seconds? minutes? days?
	- this is confusing because it doesn't take advantage of the time system
		- using an integer to represent time intervals gives you no protection against accidentally misinterpreting it
			- utilizing stronger types allows you to create stronger code

## std::chrono ##
- clocks, source of time information
	-  now - the current system time 
		- `std::chrono::system_clock::now()`
	- type - what type of value represents the time (unsigned, long, etc.)
		- `std::chrono::system_clock::time_point`
	- tick period - how often the clock ticks
		- `std::chrono::system_clock_period` 
			- if it's `std::ratio<1,100>`, it ticks 100 times per second
	- accuracy
		- is the tick period consistent or is it an average
			- `std::chrono::system_clock::is_steady`
	- duration - length of time
		- to use, cast to the type you want, then use count
			- `std::chrome::seconds(std::chrono::days(1)).count()`
			- sadly, the notation sucks
	- points in time associated with a clock
		- add and subtract time with each other to get a new time point
		- can subtract one time point from another to get a duration
			- both time points must come from the same clock
## user defined literals ##
- C++ lets you use suffixes to specify literals for built-in types
	- `auto x = tuple(1,2u,3l); // int, unsigned, long`
		- can this be applied to time classes?
			- yes
			- to get 40 milliseconds, use `40ms`
			- `1y+2d+3h+7min+5s+5us+6ns`
				- one year, two days, three hours, seven minutes, 5.002005006 seconds
			- easy to make clear you want to sleep 30 milliseconds
				- `sleep(30ms);`
## Sleeping a thread in C++ ##
- sleep for 30 seconds
	- `std::this_thread::sleep_for(30s);`
	- C++20 also gives the easy creation of time points:
```
// Groundhog sleeps until noon on Groundhog Day  
this_thread::sleep_until(sys_days{February/2/2019} + 12h);
```
- strong types can wrap the underlying number representing a duration or point in time in a safe and clear fashion with no loss in performance


# String literals #
- a C string literal is actually of type `char const *`
	- can do wrong with conversion to C++ strings
	- add s on the end, i.e. `"hello, world"s`

## How do I define my own literal? ##
- what does a temperature of 7 mean?
	- Fahrenheit, Celsius, Kelvin?
- here's an example of a temperature class:
```
struct Temp { double degrees_K; };
constexpr Temp operator"" _K(double d)  
{ return Temp{d}; }
constexpr Temp operator"" _C(double d)  
{ return Temp{d + 273.15 }; }
constexpr Temp operator"" _F(double d)  
{ return Temp{(d - 32)*5/9 + 273.15}; }
static_assert(32_F == 0_C); // Units clear
```

## Inter-thread communication ##
- sometimes locks aren't what you want
- imagine a "producer/consumer" design pattern
	- like a supply chain
	- some threads produce work items, other threads consume those work items
	- typically producers put work onto a queue, and consumers take them off
		- locks can allow thread-safe access to the queue
	- what if there's no work on the queue?
		- the consumer threads need to go to sleep and wake up when there is work to do
		- as opposed to a lock, you'd like to wait for an "event" stating the queue has become non-empty
### Producer/Consumer implementation ###
- there are a couple new library features that could be helpful
	- `unqiue_lock`
		- richer version of lock 
	- `condition_variable`
		- "wakes up" waiting threads

### Condition variables ###
- the C++ version of an event
	- `condition_variable cv;`
- you can wait for a condition variable to signal
```
mutex m;  
boolean test();  
unique_lock<mutex> lck(m);  
cv.wait(lck, test);
```
- if the test succeeds, the wait returns immediately, otherwise it locks `m`
	- this is why we need a `unique_lock` instead of a `lock_guard`
- once `condition_variable` signals the waiting thread...
	- the lock is reacquired
	- the test is rerun (if it fails, we wait again)
	- once the test succeeds, the program continues

#### signaling an event is simple ####
- `cv.notify_one();`
	- wakes one waiter
		- no guarantee to which one
- `cv.notify_all();`
	- wakes all waiters
```
mutex m;  
queue<data_chunk> data_queue;  
condition_variable cond;  
void data_preparation_thread()  
{  
while(more_data_to_prepare()) {  
data_chunk const data=prepare_data();  
lock_guard lk(m);  
data_queue.push(data);  
cond.notify_one();  
}  
}  
void data_processing_thread()  
{  
while(true) {  
data_chunk data;  
{  
unique_lock<mutex> lk(m);  
cond.wait(lk,[]{return !data_queue.empty();});  
data=move(data_queue.front());  
data_queue.pop();  
}  
process(data);  
if(is_last_chunk(data))  
break;  
}  
}
```

- it's nice that we can pass arguments to a thread (like we do to functions), but how can we get the thread to return a value back?
	- we want to be able to use threads as "asynchronous functions"
		- `std::future<int> f = std::async(func_returning_int);`
	- C++11 defines a `std::future` class template that lets a thread return a value sometime in the future when it's calculation is complete
	- one way to create a future is with `std::async`
		- as soon as you create it, it starts running the function passed to it in a new thread
		- call `get()` when you want to get the value produced by the function
			- `get()` waits for the thread function to finish, then returns the value

### Introducing Promises and Futures ###
- promises and futures let you pass values between threads
	- kind of like producer/consumer but you are just passing a single item
		- no need for a queue
	- a promise lets you pass a value from one thread to another
	- to create a paired future and promise:
```
promise<int> p;
future<int> fi = p.get_future();
```
- the producing thread stores the value in the promise
	- `p.set_value(7);`
- the consuming thread receives the value when it becomes available
	- `cout << "received" << fi.get() << endl;`
```
#include <future>  
#include <iostream>  
int calculate_the_answer_to_LtUaE();  
void do_stuff();  
int main()  
{ // Run calculation in a different thread  
std::future<int> the_answer  
= std::async(calculate_the_answer_to_LtUaE);  
do_stuff();  
std::cout <<"The answer to life, the universe and everything is "  
<< the_answer.get()  
<< std::endl;  
}
```

#### Can we check if the future has a value yet? ####
- yes
	- `f.wait_for(std::chrono::seconds(0)) == std::future_status::ready;`
		- can be less ugly if we use a literal
			- `f.wait_for(0s) == std::future_status::ready;`
- what if the asynchronous function throws an exception?
	- if the thread function in an `async` throws an exception, calling `get()` will throw the exception
		- just like if the asynchronous function was a "real" function

### Introducing packaged tasks ###
- `async` is good if you just want to run a function on a new thread to get a return value
	- what if you want to asynchronously run a function on a specific existing thread?
		- this is what `std::packaged_task` is for
		- if one thread says:
```
packaged_task<int(double)> pt{f};  
future<int> fi = pt.get_future();
```
- then you can run this in any other thread:
	- `pt(3.6);`
	- this value will then become available in `fi`

- it would be nice to have an implementation of `std::accumulate` that breaks its input up into pieces, adds up each piece in parallel, then adds up the results
	- you can do this with futures

### Are Futures an RAII class? ###
- does the destructor of a future join the thread if no one has called `get()` yet?
```
void f() {  
auto result = async(g);  
if(needResult())  
auto val = result.get();  
// If I didn’t need the result,  
// will the return block until  
// result’s destructor  
// joins g’s thread?  
return;  
}
```
- It depends!
	- if you get a future from a promise, it is "non-blocking"
		- destroying the future does not wait for the thread to complete
	- if you call `async`, it returns a "blocking future"
		- it's destructor will join the thread
	- this split leads to some surprising behavior
		- the following code runs in parallel
```
auto a = async(f);
auto b = async(g);
```
- the following code has no parallelism:
```
async(f);
async(g);
```
- since the future returned from `async` is not stored anywhere, it will be destroyed after line 1, so it's destructor will wait for `f`'s thread to complete running before it advances to the next line and launches `g`
	- this is confusing and will be fixed in a future version of C++
		- big takeaway: always call `get()` on future before destroying to (even if you don't really need to)