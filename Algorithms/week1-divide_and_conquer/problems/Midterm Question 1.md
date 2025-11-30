### Part a prompt
```
def FindMax(A[1..n]): // A[1..n] is an array of integers
	if n==0:
		return -\infty
	else if n==1:
		return A[1]
	else:
		mid = floor(n/2)
		return max(FindMax(A[1..mid]), FindMax(A[(mid+1)..n]))
```

> What is the tightest upper bound for Algorithm FindMax's time complexity in terms of n? Write a recurrence for $T(n)$, the running time of algorithm 1 as a function of input size $n$, and solve it

#### Response
- Recurrence:
	- $T(n)  = 2T(n/2) + O(n^0) = 2T(n/2) + O(1)$
		- this indicates that each iteration of this algorithm divides the input space into two subproblems, each of which receives half the input space.
			- each individual subproblem performs a constant amount of work
- Solving the recurrence:
	- by the Master Theorem
		- $a = 2$
		- $b = 2$
		- $c = 0$
		- $log_2 2 = 1$
			- $1 > 0$ from the master theorem
				- so $O(n^{log_b a}) = O(n^1) = O(n)$

### Part b prompt
```
def SumToN(n):
input: n is a nonnegative integer
if n==0:
	return 0
else:
	return n + SumToN(n-1)
```
> What is the tightest upper bound for the algorithm's complexity in terms of n? Write a recurrence for $T(n)$, the running time of algorithm 2 as a function of the input size $n$, and solve it


#### Response
- Recurrence
	- $T(n) = T(n-1) + O(1)$
- each subproblem performs a constant amount of work, and this algorithm performs $n$ total calls, so the runtime is $O(n)$

### Part c prompt
```	
FindTwoSum(A[1..n], x): // A is an array of distinct integers, x is an integer
	initialize empty hash table T
	for i=1 .. n:
		if x - A[i] in T:
			return True
		else:
			Insert(T,A[i])
	return False
```
> What is the tightest upper bound for Algorithm 3's expected time complexity in terms of $n$? Briefly justify by analyzing Algorithm 3

#### Response
- the expected time complexity of adding an entry to a hash table is $O(1)$
- the expected time complexity of checking for an entry inside a hash table is $O(1)$
	- it should be said that the actual time taken can be higher due to collisions within the hash table, which will occur when multiple entries to table hash to the same value
- each iteration of the loop performs a constant amount of work
	- as the loop performs up to $n$ total iterations, our time complexity is $O(n)$

### Part d prompt
```
def Media[1..n]: // A is an array of integers, n is odd
	let H be a new empty min-heap
	for i=1 to n:
		Min-HeapInsert(H,A[i])
	for i=1 to (n-1)/2:
		Heap-Extract-Min(H)
	return H[1]
```
> What is the tightest upper bound for Algorithm 4's time complexity in terms of n? Briefly justify. Hint: your answer must account for the fact htat the size of the heap changes in the Insert and Extract operations

#### Reponse
- the algorithm operates as follows:
	- first, the algorithm performs $n$ min-heap insert operations
		- when inserting element $i$, the size of the heap is $i-1$
			- so the total cost of inserting $i$ elements is $\Sigma_{i=1}^n O(log i)$
				- this is equal to $\Theta(n log n)$
	- then, it performs $\frac{n-1}{2}$ heap extract-mins operations
		- at the beginning of this phase, the heap has size $n$
			- after extraction $j$, the heap size is $n-j+1$
				- so the total extract cost is $\Sigma_{j=1}^{\frac{n-1}{2}} O(log(n-1))$
					- this makes the total extract cost $\Theta(\frac{n}{2} log n) = \Theta(n log n)$
	- as both phases are performed in $O(n log n)$ time, that is the total runtime