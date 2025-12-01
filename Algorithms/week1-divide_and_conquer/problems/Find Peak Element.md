---
title: "Find Peak Element"
type: problem
course: algorithms
week: 1
topic: divide and conquer
tags:
  - algorithms
  - divide-and-conquer
  - week1
  - problem
related:
  - [[week1-divide_and_conquer/concepts/Recurrences.md|Recurrences]]
  - [[week1-divide_and_conquer/Week1 Dashboard|Week1 Dashboard]]
---

# Prompt
>"You are given an array $A[1..n]$ of distinct integers that is first strictly increasing, then strictly decreasing. Such an array has exactly one \"peak\" element.
>You must design and analyze a divide-and-conquer algorithm that finds the location of the peak"

### Part a
>"Write pseudocode for an $O(log n)$ runtime divide and conquer algorithm that returns the index of the peak"

- *Idea:* this problem is tailor-made for binary search. As the array is sorted, this means binary search is a possibility, and the required runtime of $O(log n)$ does not provide for many other approaches.
- *Idea:* In the strictly increasing portion of the array, any specific index is greater than the item in the previous index, and smaller than the item in the consecutive index. In the strictly decreasing portion of the array, any specific index is smaller than the previous one, and larger than the subsequent one. The peak element is unique in that it larger than both the prior and subsequent element.

```
def FindPeak(A[1..n]):
	lo = 1
	hi = n
	while lo < hi:
		mid = floor((lo+hi)/2)
		if A[mid] < A[mid+1]:
			lo = mid + 1
		else:
			hi = mid
	return lo
```
### Part b
>"Prove the correctness of your algorithm"

- we will prove by a loop invariant
- **Loop Invariant**: at the beginning of each iteration of the loop, the peak element lies in the interval $[lo,hi]$
	- Initialization:  prior to the first iteration of the array,  is the index of the first element of the array, and  is equal to the last index of the array. The peak element must lie in or between these two elements, and the initialization step is proven.
	- **Maintenance**: there are three possible cases to consider:
		- *Case 1:* $A[mid] < A[mid+1]$
			- in this case, $A[mid]$ is in the increasing part of the array. As such, we know the peak must lie to the right in the range $[mid+1, hi]$. Updating $lo$ to $mid+1$ maintains the invariant and the maintenance step is proven.
		- *Case 2*: $A[mid] > A[mid+1]$
			- in this case, $A[mid]$ is in the decreasing part of the array. As such, we know the peak is either at $mid$ or to the left. Thus the peak lies in $[lo,mid]$. Updating $hi$ to $mid$ keeps the invariant true and the maintenance step is proven
		- as our two cases are exhaustive and have both been proven, the maintenance step is true
	- **Termination:**
		- our loop terminates when $lo = hi$
			- at this moment, the invariant ensures the peak lies in $[lo,lo]$
				- exactly one element exists in this range, which is returned, and the termination step is proven
	- with the Initialization, Maintenance, and Termination steps all proven, our Loop Invariant is correct and the algorithm is proven
### Part c
>"Write and solve the recurrence describing the running time"

- $T(n) = T(\frac{n}{2}) + O(1)$
	- we solve by the master theorem
		- $a = 1$
		- $b = 2$
		- $d = 0$
		- $log_b a = log_2 1 = 0$
		- as $d=0$, then we use case 2 which yields the following runtime:
			-  $O(n^d log_b a) = O(logn)$
