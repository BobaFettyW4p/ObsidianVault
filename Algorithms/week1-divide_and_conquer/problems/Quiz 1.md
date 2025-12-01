---
title: "Quiz 1"
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
  - [[week1-divide_and_conquer/concepts/Recursion Trees.md|Recursion Trees]]
  - [[week1-divide_and_conquer/Week1 Dashboard|Week1 Dashboard]]
---

## Part a
>"What is the asymptotic running time of the following algorithm? Write a recurrence for the running time and solve it using the master theorem."

```
def find_max(A[1..n], low, high): // 1 <= low <= high <= n
	if low == high:
		return A[low]
	m = floow(low+(high-low)/2)
	left_max = find_max(A,low,m)
	right_max = find_max(A, m+1, high)
	if left_max > right_max:
		return left_max
	else:
		return right_max
```

### Solution
- *Recurrence:* $T(n) = 2T(\frac{n}{2}) + O(1)$
	- the algorithm creates 2 subproblems, each of which receive half the problem space
		- each subproblem has a constant running time
	- by the master theorem:
		- $a = 2$
		- $b = 2$
		- $d = 0$
			- $log_2 2 = 1 > 0$
			- Thus, the runtime is $O(n^{log_b a}) = O(n)$

### Part b
>"What is the asymptotic running time of the following algorithm? Justify your answer

```
def find_max(A[1..n], low, high): // 1 <= low <= high <= n
	if low == high:
		return A[low]
	m = floow(low+(high-low)/4)
	left_max = find_max(A,low,m)
	right_max = find_max(A, m+1, high)
	if left_max > right_max:
		return left_max
	else:
		return right_max
```
- *Recurrence:* $T(n) = T(\frac{n}{4}) + T(\frac{3n}{4}) + O(1)$
	- this recurrence cannot be simplified further and this recurrence is not in a valid form to use the master theorem
	- at each level, the subproblems shrink by factors of 1/4 and 3/4
		- a branch will shrink multiplicatively, so the height of the longest branch is as follows:
			- $n -> (3/4)n -> (3/4)^2 n -> \dots -> 1$
				- this branch has a length $log_{4/3} n$ which is evaluated as $\Theta(log n)$
					- Thus, the final runtime is $T(n) = \Theta(log n)$
