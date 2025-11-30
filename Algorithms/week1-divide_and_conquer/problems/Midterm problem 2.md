## Prompt
> Given a sorted array $A[1..n]$ of $n$ distinct positive integers, find the smallest positive integer missing from A. Your algorithm must run in $O(log n)$ time to receive credit

### Examples
*Input:* [1, 2, 6, 9, 11, 15]
*Output:* the smallest missing positive integer is $3$

*Input:* [1, 2, 3, 4, 5, 6]
*Output:* the smallest missing positive integer is 7

### Part a
> Write pseudocode for a divide and conquer algorithm to solve this problem. Your algorithm must run in $O(log n)$ time and return an integer. No credit will be given for $\Omega(n)$ algorithms

#### Response
- *Idea:* this is a textbook binary search problem
	- the requirement of $O(logn)$ does not allow for very many other approaches to work within the time constraints
	- as the input array is sorted, this means that binary search is a viable approach
- *Idea:* in order to an integer to not be missing, the value of an index in the array must be equal to its 1-based index location
	- in the first array $A[1] = 1$ and $A[2] = 2$
		- if $A[3] = 3$, there would be no missing integer, but since there is, it's missing
	- in the second array, each integer is equal to its 1-based index, so there is no missing integer

##### Iterative binary search version
```
def SmallestMissingInteger(A[1..n]):
	lo = 1
	hi = n
	while lo <= hi:
		mid = floor(lo + hi)/2
		if A[mid] == mid:
			lo = mid+1
		else:
			hi = mid - 1
	return lo
```

##### Recursive binary search version
```
def SmallestMissingInteger(A[1..n], lo=1,hi=n):
	if lo > hi:
		return lo
	mid = floor((lo + hi)/2)
	
	if A[mid] == mid:
		return SmallestMissingInteger(A, mid+1, hi)
	else:
		return SmallestMissingInteger(A, lo, mid-1)
```

### Part b
> Prove that your algorithm is correct. Clearly state the inductive hypothesis or loop invariant used in your proof.

##### Iterative solution
- we prove by a loop invariant
	- **Loop Invariant:** At the beginning of each iteration of the while loop, the following statements are true:
		- For every index $i$ such that $1 \leq i < lo$, $A[i] = i$
			- i.e. no positive integer in $\{1,\dots,lo-1\}$ is missing
		- the smallest missing positive integer lies in the interval $[lo,hi+1]$
	- **Initialization**
		- prior to the first iteration of the while loop, lo is equal to 1, and so there are no integral values such that $1 \leq i < lo$, and so no integer is missing
		- the range $[lo,hi+1]$ covers every possible value for the smallest missing integer and so the loop invariant holds
	- **Maintenance**
		- over the course of the loop, we calculate the value of mid as $\lfloor \frac{lo+hi}{2} \rfloor$
			- there are two cases:
				- *Case 1:* $A[mid] == mid$
					- in this case, all $i$ such that $1 \leq i < lo$ satisfy $A[i] = i$
						- as such, the smallest missing integer must be in $[mid+1.hi+1]$ and the loop invariant is upheld
				- *Case 2:* $A[mid] > mid$
					- in this case, as the integers are distinct and the array is sorted, this means that some integer in $[1,mid]$ is missing
						- $\therefore,$ the smallest missing integer is less than $mid$ and so lies in the interval $[lo,mid]$
							- we update the search space accordingly and so the loop invariant is uphelp
				- as both cases uphold the loop invariant, the maintenance step is proven
	- **Termination**
		- the loop stops when $lo > hi$
			- when this occurs, for all $1 \leq i < lo$, we have $A[i] = i$
				- so every integer in $1..lo-1$ is present in the array
					- the smallest missing positive integer lies in $[lo,hi+1]$
						- since $lo > hi$, the interval is only the single value $lo$, which is our answer
							- and so the temrination step is proven
##### Recursive solution
- we prove by induction
	- **Base Case**: $k=0$
		- in the case of an empty array, our algorithm will return $1$, which is the smallest positive integer not included in an empty array
	- **Inductive Hypothesis**:
		- for any integers $L$ and $R$ with $1 \leq R \leq R+1$, assume:
			- all indices $i$ with $1 \leq i < L$ satisfy $A[i] = i$
			- then the algorithm returns the smallest missing positive integer for any interval of length $k$ such that $k < n$
	- **Inductive Step**:
		- let us consider an interval $lo,hi$ of length $n$
			- our algorithm will calculate the value of $mid$ for this $lo, hi$
				- *Case 1:* $A[mid] == mid$
					- in this case, all indices $< lo$ will satisfy the condition $A[i] = i$
					- $A[mid] == mid$ indicates the $mid$ index also satisfies this condition
					- we are then able to conclude that the missing value is in the range $[mid+1, hi+1]$
						- our algorithm then recursively calls itself on that range, which by the Inductive Hypothesis will return the correct value and the case is proven
				- *Case 2*: $A[mid] > mid:$
					- in this case, we can determine the lowest missing integer is smaller than $mid$
						- our algorithm will recursively calls itself on the range $[lo,mid-1]$, which by the Inductive Hypothesis will return the correct value
				- with both cases proven, and both cases covering all feasible inputs to the algorithm, the inductive step is proven