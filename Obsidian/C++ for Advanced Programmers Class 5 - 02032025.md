- you might wonder why you can create an object of type `Matrix<int,1,1>` even though `Matrix<int,1,1>::minor(int,int)` doesn't make sense
	- calling minor turns the 1x1 matrix into a 0x0 matrix, which is undefined behavior
	- if a method of a template class isn't called, then it isn't compiled
	- helpful, but why is a static member never compiled?

**CWhy**
- good tool to analyze error messages

**`static_assert`**
- compile-time assertion that prints an error message
- `static_assert(row==cols, "Sorry, only square matrices have determinants");`

- Sometimes a method needs a reference to the object it's a part of
	- `*this` can fill this role
```
template<class T, int h, int w>  
T  
Matrix<T, h, w>::determinant() const  
{  
return determinantImpl(*this);  
}
```

**Inheriting constructors**
- we can inherit everything but constructors from the common implementation even though they do the right thing
	- the constructors don't have the same names, they are named after the class
		- Since classes often have many constructors, this can make it painful to build lightweight wrapper classes on top of others
			- we can fix this by saying we want to inherit constructors:
```
class Matrix : public MatrixCommon <T, rows, cols> {
public:
using MatrixCommon<T, rows, cols>::MatrixCommon;
}
```

Concepts are a C++ template feature, which allow us to constrain templates
```
template<std::floating_point T, int m, int n=m>
struct Matrix { /* .. */ };
```

- algorithms typically require a callable
	- how can we write a template that expects its argument is callable with an int?
	- concepts allows us to create a "prototype" for a template
```
void f(std::invocable<int> auto callable) {
callable(5);
}
```

- what if we tried to create the same function without a template?
```
void f(auto callable) {
callable(5);
}
```
- this still works with a valid callable
	- if it does throw, it will throw inside the function if callable is set to an invalid value

**What goes wrong with the following natural code?**
```
template<typename T>  
void f(T &t) {  
sort(t.begin(), t.end()); // OK  
}  
vector<int> v = { 3, 1, 4, 1};  
list<int> l = {5, 9, 2};  
f(v); // OK  
f(l); // Huge compiler error
```

- `sort` requires random access iterators
	- since `list` needs to be walked through one node at a time, their iterators are not considered random access
	- leads to an incredibly messy error message
- this is a big problem!
	- C++ is a language of lightweight abstractions
		- the theory of templates is to provide the ideal abstract interface at zero runtime cost (all the cost occurs at compile time)
	- easy to solve with concepts:
```
template<random_access_iterator it>  
sort(it beg, it end);
```

- perhaps even more of a problem, sometimes templates can run but give the wrong answer
	- imagine this implementation of the Greatest Common Divisor:
```
int gcd(int a, int b) {  
if(b == 0)  
return a;  
return gcd(b, a – b*(a/b));  
}
```
- this efficiently and correctly calculates greatest common divisors
	- `gcd(48,30)==6 //true`
	- what if we want the greatest common divisor of other types
		- unsigned, long, etc.
		- crucial as this is the heart of RSA cryptography
			- templates are designed to prevent us from having to write a million gcd functions for each type
	- GCD correctly templatized:
```
template<typename T>  
T gcd(T a, T b) {  
if(b == 0)  
return a;  
return gcd(b, a – b*(a/b));  
}
```
- this works and correct calculates GCD's like `gcd(48,30)`, `gcd(48u,30u`, `gcd(481,301)` etc.
	- the real issue is lack of constraints on the template parameters
		- `gcd()` can be initialized with types that don't satisfy the assumptions on the parameters
			- `gcd(4.0,6.0)` compiles, but crashes on runtime
			- even worse, `gcd(4.0,8.0)` runs but gives an incorrect answer
				- any double divides any other double, so it makes no sense to call it the Greatest Common Divisor
- what's the takeaway?
	- in the original Kernigan & Ritchie version of C, function prototypes didn't need argument lists
		- `int gcd();`
		- always easy to inadvertently call with inappropriate arguments
			- `gcd(12l,16l); //this crashes`
		- the solution was to require prototypes in declarations to ensure valid arguments
			- `int gcd(int, int);`
	- what we really need are prototypes for templates
		- C++20 lets us do this
```
template<integral T>  
T gcd(T a, T b) {  
if(b == 0)  
return a;  
return gcd(b, a – b*(a/b));  
}
```
- what can we do in classic C++?

**SFINAE**
- Substitution Failure is Not an Error
	- when creating candidate overloads for a function, invalid substitutions in a template should mean it's not considered a possible overload
		- No compile error occurs even though the template failed to compile
	- when writing SFINAE code, use `std::enable_if` to make it suck less
```
struct Test {  
typedef int foo;  
};  
template <typename T>  
void f(typename T::foo) {} // Definition #1  
template <typename T> void f(T) {} // Definition #2  
int main() {  
f<Test>(10); // Call #1.  
f<int>(10); // Call #2.  
// Without error (even though there is no int::foo)  
// thanks to SFINAE.  
}
```

`std::enable_if`
```
template <bool B, class T = void>  
struct enable_if {  
typedef T type;  
};  
template <class T>  
struct enable_if<false, T> {};  
template<bool B, typename T>  
using enable_if_t = enable_if<B, T>::type;
```

- how can we use this to fix our `gcd()` template?
	- we only want `gcd()` to be used with integral types
		- the standard library has `is_integral_v` variable template that checks if a type is integral
			- `is_integral_v<unsigned>` is `true`
			- `is_integral_v<double>` is `false`
			- this allows `gcd()` to only be called on integral types
```
template <class T, typename = enable_if_t<is_integral_v<T>>>  
T gcd(T a, T b) {  
if(b == 0) return a;  
return gcd(b, a – b*(a/b));  
}
```
- `enable_if<b, T>::type` is the type `T` if `b` is `true`
- `enable_if<b, T>""type` is a substitution failure if `b` is `false`
	- you can use these in template parameter lists, return values or argument types
		- this will suppress our ability to generation certain template functions by SFINAE
```
gcd(4, 6); // OK, runs the way we expect
gcd(4.2, 6.0); // Must be a different gcd, compiler will search for another gcd() to call
```

**constexpr**
- allows you to do computations at compile-time
- Normally, we think of programming as a way to write code that runs at runtime
	- increasingly common to take place at compile time instead
- Template arguments must be known at compile time
	- this code doesn't compile:
```
int n;  
cout << "How big a matrix? ";  
cin >> n;  
Matrix<n, n> m; // Ill-formed!
```
- since we don't know `n` at compile-time, the compiler can't compile the class `Matrix<n,n>`
	- does this example compile?
```
auto square(int x) { return x*x; }  
Matrix<square(3), square(3)> m;
```
- it does not
	- while we can see that `square(3)` will evaluate to `9`, the compiler can't make that assumption
		- the code would be legal if the optimizer ran `square` at compile time, but invalid if ran at runtime
			- if something else changed `square`, it might not be computable at compile-time
			- thus, the compiler has to reject `square(3)` as a template argument
	- compile-time values can also help performance
	- why compute this every time the function is run?
		- `double pi = 4 * atan(1);`
- a constant expression is one that can be evaluated at compile-time
	- since templates and performance are so important, we would like to be able to create out own constant expressions
		- `constexpr` does this for us
- we've already seen `constexpr` variables
	- `int constexpr seven = 7; //immutable`
		- wherever the compiler sees `seven`, it can substitute the constant expression `7`
		- `Matrix<seven, 7> m; // OK`
- we also have constexpr functions
	- `double const pi = 4*atan(1)`
		- wouldn't it be nice to calculate this at compile-time?
			- can do by labelling it as `constexpr`
				- Ex. calculate GCD by Euclidian Algorithm:
```
int constexpr gcd(int a, int b) {
return b == 0? a: gcd(b, a%b);
}
```
- constexpr has its limitations
	- uninitialized declarations
	- `static` or `thread_local` declarations
	- modification of objects that were not created in the function
		- or potential modifications by calling a non-constexpr function
	- virtual methods
	- non-literal return types or parameters
		- a type is literal if its constructor is trivial or `constexpr`
- fixing our square example from earlier:
```
auto square(int x) constexpr  
{ return x*x; }  
Matrix<square(3), square(3)> m; // now this is OK
```

**`if constexpr`**
- our matrix examples might seem like overkill
	- why couldn't we just check if it's a 1x1 matrix then go from there, like this:
```
template<int r, int c = r>  
class Matrix {  
/* ... */  
double determinant() {  
if(r == 1 && c == 1) // 1x1 Matrix  
return data[0][0];  
else {  
double val = 0; // Bigger. Use formula  
for (int i = 0; i < r; i++) {  
val += (i % 2 ? -1 : 1) * data[i][0]  
* minor(i, 0).determinant();  
}  
}  
return val;  
}
```

- this won't compile
	- the `else` branch takes a minor of 1x1 matrix, which doesn't make sense
		- even though the `else` branch won't run for a 1x1 matrix, the compiler still needs to compile it
			- sadly, `Matrix<1,1>::minor(i,0)` is illegal
			- `constexpr` fixes this
- `if constexpr` as opposed to just `if` tells the compiler not to compile the branch not run if the condition is known at compile-time
	- all that's needed
	- using `if constexpr` to fix the above example looks like this:
```
template<int r, int c = r>  
class Matrix {  
/* ... */  
double determinant() {  
if constexpr (r == 1 && c == 1) // 1x1 Matrix  
return data[0][0];  
else {  
double val = 0; // Bigger. Use formula  
for (int i = 0; i < r; i++) {  
val += (i % 2 ? -1 : 1) * data[i][0]  
* minor(i, 0).determinant();  
}  
}  
return val;  
}
```
**const v constexpr**
- easy to confuse, but mean very different things
	- `constexpr` means the value is known at compile time
		- `int constexpr seven = 7;`
	- `const` means the value will not be changed
		- `void f(int const &i);`
		- we won't know the value of `i` until runtime, but we know `f` won't  change it
		- 
**`const` methods**
- if you have a `const object`, you are only allowed to call `const` methods on it
```
class A { A(); void f() const; void g(); };  
A a;  
a.f(); // OK  
a.g(); // OK  
A const ac;  
ac.f(); // OK  
ac.g(); // ill-formed
```
- this is very different from `constexpr` because the value of `ac` is not known at compile time
- it is also possible to overload methods on constness

**decltype**
- sometimes you don't know what type an expression is
	- very common with template code or complex expressions
	- `decltype(X)` gives you the type of X
		- `Matrix<decltype(x), 1, 2> m = { {x,x} };`
	- be very careful about passing pointers or references to local variables into thread functions unless you are sure the local variables won't go away during thread execution:
```
void h(int &i);  
void f() {  
int i;  
thread t(h, ref(i));  
bar(); // What if bar throws an exception?  
t.join(); // This join is skipped  
} // h keeps running with a pointer  
// to a variable that no longer exists  
// Undefined (but certainly bad) behavior
```
- better to use `try/catch`, even better to use an RAII class that joins like `std::jthread`

**Exceptions and RAII**
- When something goes wrong, throw an exception
	- ensure they're not inadvertently ignored
- Can throw an exception with `throw`
	- usually should inherit from `std::exception`
- can catch an exception with `try/catch`
	- if you don't catch the exception, it is passed to the caller and to the caller's caller, etc. until it is caught
- C++20 introduced `std::expected` that builds on previous code

**Why do exceptions break explicit resource management?**
- exceptions make it really hard to manage resources and memory:
```
void f() {
code_to_create_resources;
g(); //May throw an exception
code_to_release_resources;
}
```
- in this case, the code to release resources is never called if g() throws an exception
	- this leads to a memory leak
- searching for a better solution to memory management requires a full understanding of the C++ object lifecycle
	- In general, C++ programmers who understand the C++ object lifestyle spend very little time on memory management

**RAII**
- "Resource Acquisition is Initialization"
	- one of the most important idioms in modern C++
- RAII use automatic duration objects to manage the lifetimes of dynamic duration resources
	- ensure an automatic object's destructor performs needed cleanup

- An object's destructor is always called at the end of it's life
- `std::unique_ptr`'s destructor destroys the object it's managing and releases the memory:
```
void f() {  
auto x = make_unique<X>();  
g(); // No resource leak even if g throws  
}
```

Sometimes you want an object to outlive the object that manages it
- move ownership to another RAII object
```
unique_ptr<X> f() {  
auto x = make_unique<X>();  
g();  
return move(x); // Transfer ownership to caller  
}  
void h() {  
unique_ptr<X> h_x = f();  
/* Use the X constructed in f(). It will  
be destroyed by h_x's destructor when  
h finishes */
```

- many classes we know about use their destructor to release the object they manage (and thus are conducive to using RAII to manage memory)
	- `unique_ptr`
	- `shared_ptr`
	- `jthread`
	- `lock_guard`
	- `scoped_lock`
	- `unique_lock`
	- `shared_lock`
	- many of these are threading classes
		- concurrency makes resource management difficult

**Threads**
- computers are not getting faster
	- computer cores have not gotten any faster over the past 15 years
		- increasing clock speed increases heat
		- however, modern computers have many cores to run computations in parallel
			- thus making them faster
		- in conclusion, modern code must run in parallel across multiple cores to achieve modern speeds
- threads example:
```
#include <iostream>  
#include <thread>  
void hello_threads() {  
std::cout<<"Hello Concurrent World\n";  
}  
int  
main(){  
// Print in a different thread  
std::thread t(hello_threads);  
t.join(); // Wait for that thread to complete  
}
```

- constructing an object of type `std::thread` launches a new thread, running the callable given in the argument
	- joining the thread means the function will wait until the thread completes
		- if you don't it is possible for the function to exit, orphaning your thread (and it's output)
		- detached threads are an exception and do not need to be joined
**Locks**
- the simplest way to protect shared data is with a `std::mutex` (mutual exclusion)
- accessing the same data from multiple threads without using synchronization (like mutexes) is called a data race error
- C++ includes a handy RAII class `std::lock_guard` to ensure a thread will only release the mutex when it's done
- `std::scoped_lock` was added in C++17
	- can lock any number of locks without deadlocking
```
std::list<int> some_list; // A data structure accessed by multiple threads  
std::mutex some_mutex; // This lock prevents concurrent access to the shared data structure  
void  
add_to_list(int new_value)  
{  
// Since I am going to access the shared data struct, acquire the lock  
some_mutex.lock();  
some_list.push_back(new_value);  
// Now it is safe to use some_list.  
/* ... */  
some_mutex.unlock();  
}  
bool  
list_contains(int value_to_find)  
{  
some_mutex.lock(); // Must lock to access some_list  
bool found = std::find(some_list.begin(),some_list.end(),value_to_find) != some_list.end();  
some_mutex.unlock();  
return found;  
}
```

- this code isn't great
	- it's awkward
		- we can't just return the result of `find`
			- we store it in a local variable, release the lock, then return the local
	- we could forget to unlock
		- which is very easy and locks the application
	- an exception could bypass the unlocking code
		- `list::push_back` may throw a `bad_alloc` exception if it can't allocate space for the new object
			- this bypasses the unlocking code and locks the application

- best practice is to use RAII to write safe code
	- RAII can cleanup memory management:
```
void f() {  
auto a = make_unique<A>();  
code_to_work_with_a  
}
```

- `A` is guaranteed to have its destructor called and memory cleaned up even if an exception is thrown
- this code is also simpler and less error-prone because we no longer need to write code to clean up `a`

- `lock_guard` is an RAII class for managing locks
	- similar to how `unique_ptr` manages objects
	- its constructor acquires a lock
	- its destructor releases a lock:
```
std::list<int> some_list; // A data structure accessed by multiple threads  
std::mutex some_mutex; // This lock prevents concurrent access to the shared data structure  
void  
add_to_list(int new_value)  
{  
// Since I am going to access the shared data struct, acquire the lock  
std::lock_guard guard(some_mutex); // CTAD deduces lock_guard<mutex>  
some_list.push_back(new_value);  
// Now it is safe to use some_list. lock_guard destructor releases lock at end of function  
/* ... */  
}  
bool  
list_contains(int value_to_find)  
{  
std::lock_guard guard(some_mutex); // Must lock to access some_list  
return std::find(some_list.begin(),some_list.end(),value_to_find) != some_list.end();  
}
```

- `shared_mutex` can be acquired in shared ownership mode or unique ownership mode:
```
shared_mutex sm;  
shared_lock sl(sm);  
// RAII for acquiring sole ownership  
unique_lock ul(sm);
```

- reader-writer lock
	- multiple threads can read from a data structure as long as no thread is modifying it
	- if a thread is modifying it, it needs to acquire sole ownership so no thread sees the data in a partially modified state
```
std::list<int> some_list;  
std::shared_mutex some_mutex;  
void  
add_to_list(int new_value)  
{  
std::unique_lock guard(some_mutex); // Unique writer access  
some_list.push_back(new_value);  
}  
bool  
list_contains(int value_to_find)  
{  
std::shared_lock guard(some_mutex); // Shared reader access  
return  
std::find(some_list.begin(),some_list.end(),value_to_find) != some_list.end();  
}
```

**Lock ordering**
- if you want to avoid deadlocks, you want to acquire locks in the same order
	- to not do so could lead to a deadlock
		- thread 1 has acquired lock A but is trying to acquire lock B while thread 2 has acquired lock B but is trying to acquire lock A
			- neither will succeed
	- best practice is to document an order on your locks and always acquire them consistent with that order
		- ThreadSanitizer can help find potential deadlocks and data races
	- it's not always this simple:
```
class account {  
mutex m;  
currency_value balance;  
public:  
friend void transfer(account& from,account& to,  
currency_value amount) {  
lock_guard lock_from(from.m);  
lock_guard lock_to(to.m);  
from.balance -= amount;  
to.balance += amount;  
}  
};
```

`std::scoped_lock` acquires multiple locks at the same time and guarantees there is no deadlock
- tries releasing locks and acquiring in different orders until no deadlock occurs
```
class account {  
mutex m;  
currency_value balance;  
public:  
friend void transfer(account& from,account& to,  
currency_value amount) {  
scoped_lock lck(from.m, to.m);  
from.balance -= amount;  
to.balance += amount;  
}  
};
```

**thread arguments**
- you can add arguments to be passed to the new thread when you construct the `std::thread` object
	- there are some surprising gotchas that make them different
- passing arguments to a thread:
```
mutex io_mutex;  
void hello(string name) {  
lock_guard<mutex> guard(io_mutex);  
cout <<"Hello, " << name << endl;  
}  
int  
main(){ // No parens after thread function name:  
vector<string> names = { "John", "Paul"};  
vector<thread> threads;  
for(auto name : names) {  
threads.push_back(thread(hello, name));  
}  
for(auto it = threads.begin(), it != threads.end(); it++) {  
it->join();  
}  
}
```

- a different notation is used from arbitrary function calls, but otherwise fairly straightforward looking:
```
void f(int i);  
f(7); // Ordinary call  
thread(f, 7);// f used as a thread function
```

- however, signatures of thread functions silently "change"
```
void f(int &i) { i = 5; }  
int main() {  
int i = 2;  
std::thread t(f, i);  
t.join();  
cout << i << endl;  
return 0;  
}
```

- the above function will experience a compile error
	- "5" is intended
- thread arguments are not interpreted exactly the same way as just calling the thread function with the same arguments
- `thread`'s constructor looks like this:
```
struct thread { ...  
template<typename func, typename... arg>  
thread(func f, arg... a);  
...  
};  
...  
// Deduces thread::thread<void(*)(int&), int)  
std::thread t(f, i);  
...
```

- templates don't know if it takes its argument by reference
```
void f(int &i) { i = 5; }  
int main() {  
int i = 2;  
std::thread t(f, std::ref(i));  
t.join();  
cout << i << endl;  
return 0;  
}
```



