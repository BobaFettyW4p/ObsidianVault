## CLRS Ch. 1,2,3, 4.1, 4.3-4.5

### Chapter 1
#### 1.1 Algorithms
- an *algorithm* is any well-defined computational procedure that takes some value, or set of values, as *input* and produces some value or set of values as *output*
	- thus, an algorithm is a set of a computational steps that transform input into output
- we can also view an algorithm as a tool to solve a computational problem
	- e.g. sorting
- Let us formally define the *sorting problem*:
	- **Input**: A sequence of $n$ numbers $(a_1, a_2,\dots, a_n)$
	- **Output**: A permutation (reordering) $(a'_1, a'_2, \dots, a'_n)$ such that $a'_1 \leq a'_2 \leq \dots \leq a'_n$
	- e.g. an input of (31, 41, 59, 26, 41, 58) will return the output (26, 31, 41, 41, 58, 59)
		- an input sequence like this is referred to as an *instance* of the sorting problem
			- in general, an *instance of a problem* consists of the input needed to compute a solution to a problem
- sorting is a fundamental operation in computer science
	- many programs use it as an intermediate step
	- thus, there are many good sorting algorithms available
		- which is best can depend on many factors
			- number of items to be sorted
			- extent to which items are already sorted
			- restrictions on item values
			- computer architecture or storage devices available
- an algorithm is said to be *correct* if for every input instance, it halts with the correct output
	- a correct algorithm *solves* the given computational problem
		- incorrect algorithms can be useful provided we are able to control their error rate
##### What kinds of problems are solved by algorithms?
- there are many more besides sorting
	- the Human Genome Project
	- Internet routing
	- eCommerce
	- manufacturing and resource allocation
##### Data structures
- a *data structure* is a way to store and organize data to facilitate access and modifications
	- no one data structure works well for all purposes
		- important to know strengths and limitations of all of them
##### Hard problems
- mostly about efficient algorithms
	- a usual measure of efficiency is speed
		- how long an algorithm takes to produce a result
- NP-Complete problems
	- no efficient algorithm has ever been found, but nobody has ever proven that such an algorithm cannot exist
	- if an efficient algorithm exists for any NP-Complete problem, that would mean an algorithm exists for all of them
	- several NP-complete problems are very similar to problems that have known efficient algorithms
	- arise surprisingly often in real applications
		- if you can prove a problem in NP-complete, you can instead develop an efficient algorithm that gives a good solution without worrying about finding the best possible solution
##### Parallelism
- for many years, processor clock speeds increased at a steady rate
	- physical limitations prevented this from continuing forever
		- power density increases superlinearly and thus chips run the risk of melting once their clock speeds get high enough
			- solved this by creating multiple processing cores
- in order to take advantage of this, algorithms must be designed with parallelism in mind
#### 1.2 Algorithms as a technology
- if computers were infinitely fast, any correct method would suffice
	- but computers are not infinitely fast
		- memory is cheap, but it is not free
##### Efficiency
- different algorithms devised to solve the same problem can offer radically different efficiency
	- e.g. *insertion sort* vs. *merge sort*
##### Algorithms and other technologies
- we should consider algorithms as a *technology*
	- total system performance depends on choosing efficient algorithms as well as fast hardware
		- rapid advances are being made in algorithms, similar to hardware
### Chapter 2

#### 2.1 Insertion Sort
- **Input**: A sequence of $n$ numbers $(a_1, a_2, \dots, a_n)$
- **Output**: a permutation (reordering) $(a'_1, a'_2, \dots, a'_n)$ of the input sequence such that $a'_1 \leq a'_2 \leq \dots \leq a'_n$
- the numbers we wish to sort are known as the *keys*
	- conceptually, we are sorting a sequence, the input is an array with $n$ elements
- insertion sort is an efficient algorithm for sorting a small number of elements
	- we start with the elements in a random order, we take one element at a time and sort it into the correct order
		- once all elements have been placed in their correct spot, the input is ordered
![[Pasted image 20250925202010.png]]

![[Pasted image 20250925202142.png]]

- the index $j$ indicates the "current card" being inserted into the hand
##### Loop invariants and the correctness of insertion sort
- at the beginning of each iteration of the $for$ loop:
	- the "current card", denoted by $j$
	- the subarray $A[1 \dots j-1]$, which indicates the "currently sorted hand"
	- the remaining subarray $A[j+1 \dots n]$ constituted the pile of cards still on the table, waiting to be sorted
- we are able to state these properties of $A[1 \dots j-1]$, which are known as a *loop invariant*:
> "At the start of each iteration of the **for** loop, the subarray $A[1 \dots j-1]$ consists of the elements originally in $A[1 \dots j-1]$ but in sorted order"
- loop invariants are used to help us understand why an algorithm is correct
- we must show three things about a loop invariant:
	- **Initialization**: It is true prior to the first iteration of the loop
	- **Maintenance**: If it is true before an iteration of the loop, it remains true before the next iteration
	- **Termination**: When the loop terminates, the invariant gives us a useful property that helps show that the algorithm is correct
- if the first two properties hold, the loop invariant is true prior to every iteration of the loop
	- we do not have to prove both if we can prove the loop invariant is true in other ways
- the third property is the most important one when we are trying to prove an algorithm is correct
	- typically use the loop invariant along with what causes a loop to terminate

- **Initialization**
	- we show the loop invariant holds before the first loop iteration when $j = 2$
	- The subarray $A[1 \dots j-1]$ contains just the single element $A[1]$
		- this is just the original element in $A[1]$
			- as it consists of one element, it is trivially sorted
				- $\therefore$,  the loop invariant holds prior to the first iteration of the loop
- **Maintenance**
	- The body of the **for** loop works by moving an item down the loop until it finds the proper position for $A[j]$, at which point it will progress to the next value in the array
		- the subarray contains the elements in $A[1 \dots j]$ but sorted
			- $\therefore$ the **for** loop preserves the loop invariant
- **Termination**
	- The condition causes the **for** loop to terminate when $j < A.length = n$
	- At the end, we have the subarray $A[1 \dots n]$ which consists of all the elements originally in $A$ but in sorted order
		- as this subarray is the entire array, we can conclude the entire array is sorted and thus, the algorithm is correct
#### 2.2 Analyzing algorithms
- analyzing an algorithm means predicting the resources that the algorithm requires
	- memory
	- communication bandwidth
	- computer hardware
	- most often computational time is most important
- by analyzing several candidate algorithms, we can often identify a most efficient one
- before analysis, we must have a model of the implementation technology it will use
	- we can assume a generic one process random-access machine (RAM) modelof computation
		- our algorithms are computer problems

##### Analysis of insertion sort
- the time varies based on the number of items that need to be sort
	- sorting 5 items is faster than sorting 1000 items
	- two sequences of the same size can take longer to sort based on how close fully sorted they are to begin with
- In general, time taken by an algorithm increases with the size of the input
	- it is traditional to describe the running time of a program as a function of the size of its input
		- thus, we need to define *running time* and *size of input* more carefully
- the way to conceive **input size** depends on the problem being studied
	- for most problems, the most natural measure is the number of items in the input, e.g. the size of the array $n$ being sorted by insertion sort
		- e.g. when describing multiplying two integers, the most natural input size is the total number of bits used to represent the integers
		- if the input is a graph, it may be best represent the input size as the number of edges and number of vertices
- The **running time** is the number of primitive operations or "step" executed
	- a step should be defined such that it is as machine-independent as possible
![[Pasted image 20250926195441.png]]

- the running time can be calculated by summing each row in the cost and times columns
![[Pasted image 20250926200116.png]]
##### Worst case and average case scenarios
- when analyzing an algorithm, you can look at the *best-case scenario* (in insertion sort, this is when the array is already sorted)
- however, most of the time we will look at the *worst-case scenario*
	- this is the longest running time for any input of size $n$
	- this gives us an upper bound on running time
		- an algorithm is guaranteed to never take longer
	- for some algorithms, this occurs fairly often
	- the average case is often roughly as bad as the worst case
		- average case is often limited, as it may not be apparent what an "average" input is for a particular problem
		- we may often assume that all inputs of a given size are euqally likely
			- this may be violated, but we can sometimes use a randomized algorithm to allow a probabilistic analysis and yield an expected running time
##### Order of growth
- it is the *rate of growth* of an algorithm that is most interesting to us
	- let us consider a rate of growth of $an^2 + b$
		- in this case, we can ignore the lower order term ($b$) as well as the constant coefficient ($a$)
			- as $n$ grows sufficiently large, they become insignificant in comparison
			- $\therefore$, we would write that insertion sort has a worst case run time of $\Theta (n^2)$
	- one algorithm is generally considered more efficient if it's worst case running time has a lower order of growth than another
#### 2.3 Designing algorithms
##### Divide and conquer

- many algorithms are *recursive* in structure: to solve a problem, they call themselves one or more times to deal with several subproblems
	- these algorithms typically follow a *divide and conquer* approach:
		- they break the problem into several subproblems that are similar, but only encapsulate a portion of the problem, solve these problems recursively, then combine the so
- Design and conquer involves three steps:
	- **Divide** the problem into a number of subproblems that are smaller instances of the same problem
	- **Conquer** the subproblems by solving them recursively.
		- if the subproblem is small enough, they may be able to be solved in a straightforward manner. Otherwise we can recursively divide and conquer again
	- **Combine** the solutions to the subproblems into the solution for the original problem
- insertion sort works as follows:
	- Divide the $n$-element sequence into two subsequences of $\frac{n}{2}$ elements each
	- Conquer: Sort the two subsequences recursively using merge sort
	- Combine: Merge the two sorted subsequences to produce the sorted answer
- The recursion "bottoms" out once the sequence to be sorted has length 1
	- every sequence of length 1 is trivially sorted
- they key step of insertion sort is the merge
![[Pasted image 20250927143508.png]]

![[Pasted image 20250927143518.png]]

![[Pasted image 20250927143538.png]]
#### Analyzing divide and conquer algorithms
-  when an algorithm contains a recursive call to itself, we can often describe its running time by a *recurrence equation* or *recurrence*
	- a *recurrence* describes the overall running time on a problem of size $n$ in terms of the running time on smaller inputs
		- we can use mathematical tools to extrapolate to the entire problem
![[Pasted image 20250927145024.png]]

![[Pasted image 20250927145046.png]]

### Growth of Functions
#### Asymptotic notation

- generally used to describe the running times of algorithms
	- i.e. insertion sort's worst-case runnign time is $\Theta (n^2)$
	- asymptotic notation applies to functions, not algorithm
		- i.e. insertion sort's actual running time is $an^2 +bn + c$, but we abstract everything away to yield the running time of $\Theta (n^2)$
		- in general, we will apply asymptotic notation to the running time of algorithms, but it can also apply to functions that characterize other aspects of algorithms as well
			- e.g. how much space they use
		- must be careful to specify which running time is referred to by asymptotic notation
		- we often wish to characterize running time independent of input
			- we want to make a blanket statement that covers all inputs, not just the worst case
##### $\Theta$ notation
- The worst case running time of insertion sort is $T(n) = \Theta(n^2)$
	- for a given function $g(n)$, $\Theta (g(n))$ represents the set of functions
![[Pasted image 20250927150936.png]]

![[Pasted image 20250927150945.png]]

- a function $f(n)$ belongs to the set $\Theta(g(n))$ if there exist positive constants $c_1$ and $c_2$ such that $f(n)$ can be "sandwiched" in between $c_1g(n)$ and $c_2g(n)$ for sufficiently large $n$
- for all $n \geq n_0$, $f(n)$ is equal to $g(n)$ within a constant factor
	- $g(n)$ is an *asymptotically tight bound* for $f(n)$
- the definition of $\Theta(g(n))$ requires that every $f(n) \in \Theta(g(n))$ be *asymptotically nonnegative*
	- $f(n)$ for sufficiently large values of $n$ is nonnegative
- an *asymptotically positive function* is positive for all sufficiently large values of $n$
##### O-notation
- $\Theta$ notation asymptotically bounds a function from above and below
	- $O$-notation is used when we only have an asymptotic upper bound
- We denote $O$-notation as follows:
![[Pasted image 20250927152519.png]]

- $O$-notation is used to give an upper bound within a function within a constant factor
- $f(n) = \Theta(g(n))$ implies $f(n) = O(g(n))$ as $\Theta$-notation is stronger than $O$-notation
- with $O$-notation, we can describe the running time of an algorithm by inspecting the algorithm's overall structure
	- e.g. the double-nested loop of insertion sort yields an $O(n^2)$ upper bound on the worst-case running time
- since $O$-notation describes an upper bound, when we use it to bound the worst-case running time of an algorithm, we have a total total bound of the running time of the algorithm regardless of output 
	- technically, it's an abuse to say the running time of an algorithm is the $O$-notation time, as the actual running time will vary based on the input
		- we mean that there is a function $f(n)$ that is $O(n^2)$ such that for any value of $n$, regardless of the input chosen, is bounded from above by the value $f(n)$
##### $\Omega$-notation
- just as $O$-notation provides an asymptotic upper bound on a function, $\Omega$-notation provides an *asymptotic lower bound*
	- for a given function $g(n)$, we denote $\Omega(g(n))$ as follows:
![[Pasted image 20250927154937.png]]

- Theorem 3.1
>For any two functions $f(n)$ and $g(n)$, we have $f(n) = \Theta(g(n))$ if and only if $f(n) = O(g(n)) \land f(n) = \Omega(g(n))$

- when we ay the running time of an algorithm is $\Omega(g(n))$, we mean that no matter what input of size $n$ is select, the running time is at least a constant times $g(n)$ for sufficiently large $n$
	- we are providing a lower bound on the best-case running time of an algorithm

##### $o$-notation
- the asymptotic upper bound provided by $O$-notation may or may not be asymptotically tight
	- e.g. $2n^2 = O(n^2)$ is asymptotically tight, but $2n = O(n^2)$ is not
	- $o$-notation is used to denote an upper bound that is not asymptotically tight
![[Pasted image 20250927155758.png]]

- intuitively, $f(n)$ becomes insignificant as $g(n)$ approaches infinity
![[Pasted image 20250927155931.png]]

##### $\omega$-notation
- similar to $o$-notation, but for $\Omega$-notation
	- denotes a lower bound that is not asymptotically tight
![[Pasted image 20250927160246.png]]
- this implies
![[Pasted image 20250927160257.png]]

##### Comparing functions

![[Pasted image 20250927160330.png]]

![[Pasted image 20250927160338.png]]

![[Pasted image 20250927160346.png]]

![[Pasted image 20250927160352.png]]


#### Standard notations and common functions
##### Monotonicity
- a function $f(n)$ is *monotonically increasing* is $m \leq n$ implies $f(m) \leq f(n)$
	- it is *monotonically decreasing* if $m \leq n$ imples $f(m) \geq f(n)$
- a function $f(n)$ is *strictly increasing* if $m < n$  implies $f(m) \geq f(n)$
	- it is *strictly decreasing* if $m < n$ implies $f(m) > f(n)$

##### Floors and ceilings
- for any real number $x$, we denote the greatest integer less than or equal to $x$  by $\lfloor x \rfloor$
	- the least integer greater than or equal to $x$ is denoted as $\lceil x \rceil$
- for all real $x$:
	- $x-1 < \lfloor x \rfloor \leq x \leq \lceil x \rceil < x+1$
- for any integer $n$:
	- $\lceil \frac{n}{2} \rceil + \lfloor \frac{n}{2} \rfloor = n$
- for any real number $x \geq 0$ and integers $a,b > 0$
![[Pasted image 20250927161448.png]]
- the floor function $f(x) = \lfloor x \rfloor$ is monotonically increasing
	- as is the the ceiling function $f(x) = \lceil x \rceil$

##### Modular arithmetic
- for any integer $a$ and any positive integer $n$, $a \mod n$ is the remainder of the quotient $a / n$
	- $a \mod n = a - n \lfloor a/n \rfloor$
	- $\therefore, 0 \leq a \mod n < n$

##### Polynomials
- given nonnegative integer $d$, a *polynomial in $n$ of degree $d$* is a function $p(n)$ of the form:
	- $p(n) = \sum_{i=0}^d a_in^i$
		- the constants $a_0, a_1, \dots, a_d$ are the *coefficients* of the polynomial and $a_d \neq 0$
- a polynomial is asymptotically positive if and only if $a_d > 0$
- for any real constant $a \geq 0$, the function $n^a$ is monotonically increasing
	- for any real constant $a \leq 0$, the function $n^a$ is monotonically decreasing
- a function $f(n)$ is *polynomially bounded* if $f(n) = O(n^k)$ for some constant $k$
##### Exponentials
- for all $n \land a \geq 1$, the function $a^n$ is monotonically increasing in $n$

![[Pasted image 20250927162911.png]]

- for all constants $a,b$ such that $a>1$,
	- $\lim_{n\to\infty} \frac{n^b}{a^n} = 0$
		- we are able to conclude that $n^b = o(a^n)$
- any exponential function with a base strictly greater than 1 grows faster than any polynomial function

##### Logarithms
![[Pasted image 20250927163411.png]]
- logarithm functions apply only to the next term in the formula
	- $ln\ n + k$ = $(ln\ n) + k$
![[Pasted image 20250927163534.png]]
- changing the base of a logarithm from one constant to another changes the value of the logarithm by a constant factor
	- we will often use $lg\ n$ when we don't care about constant factors
- a function $f(n)$ is *polylogarithmically bounded* is $f(n) = O(lg^k\ n)$
##### Factorials
![[Pasted image 20250927165600.png]]
- a weak upper bound is $n! \leq n^n$
- Stirling's approximation:
![[Pasted image 20250927165729.png]]

##### Functional iteration
- we use $f^{(i)}(n)$ to denote the function $(f(n))$ iteratively applied $i$ times to an initial value of $n$


### Divide and Conquer

- divide and conquer solves a problem recursively in the following steps:
	- **Divide** the problem into a number of subproblems that are smaller instances of the same problem
	- **Conquer** the subproblems by solving them recursively
		- if they're small enough, solve them in a straightforward manner
	- **Combine** the solutions to the subproblems into the solution for the original problem
- when problems are large enough to solve recursively, this is referred to as the *recursive case*
	- when the subproblems become small enough that we no longer recurse, this is known as the *base case*
##### Recurrences
- a *recurrence* is an equation or inequality that describes a function in terms of its value on smaller inputs
- recurrences can take many forms
	- a recursive algorithm can divide subprocesses into unequal sizes, such as a $2/3$ to $1/3$ split
- subproblems are not necessarily constrained to being a constant fraction of the original problem size
- Three main methods for solving recurrences:
	- *substitution method* - guess a bound and use mathematical induction to prove our guess correct
	- *recursion-tree method* - convert the recurrence into a tree whose nodes represent the costs incurred at various levels of recursion
		- use techniques to bound summations to solve the recurrence
	- *master method* - provide bounds for recurrences of the form:
		- $T(n) = aT(n/b) + f(n)$
			- where $a \geq 1, b > 1$ and $f(n)$ is a given function
#### The substitution method for solving recurrences
- is comprised of two steps:
	- guess the form of the soltuion
	- use mathematical induction to find the constants and show the solution works
	- 

## Dasgupta 2.1-2.3