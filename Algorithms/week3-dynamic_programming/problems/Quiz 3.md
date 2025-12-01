---
title: "Quiz 3"
type: problem
course: algorithms
week: 3
topic: dynamic programming
tags:
  - algorithms
  - dynamic-programming
  - week3
  - problem
related:
  - [[week3-dynamic_programming/concepts/Dynamic Programming.md|Dynamic Programming]]
  - [[week3-dynamic_programming/Week3 Dashboard|Week3 Dashboard]]
---

# Prompt
> "You are given an array $A[1..n]$ of $n$ positive integers. We say a subset of elements of $A$ is valid if it does not contain consecutive elements of the array"
> Describe a dynamic programming algorithm that, given $n$ and the array $A[1..n]$, determines the maximum sum of elements in a valid subset of $A$.
> Ex. If $n=5$ and $A=[4, 10, 3, 1, 2]$, the maximum possible sum in a valid subset is 12. We can achieve this by choosing the second and fifth elements in the array.

### Part a
>"The algorithm will create an array $DP[1..n]$. Describe what subproblem $DP[i]$ represents.

- $DP[i]$ represents the maximim possible sum in a valid subset of an input array $A[1..i]$

### Part b
>"Define the base case(s) of the DP algorithm.

- our first base case is $n=0$
	- An empty array has a maximum valid subset with a value of $0$
- our second base case is $n=1$
	- A valid subset of an array of size 1 cannot contain consecutive elements,a s there is only one element it can contain
		- $DP[1] = A[1]$

### Part c
> "Write the recurrence of the DP algorithm"

- $DP[i] = max(DP[i-1], DP[i-2]+A[i])$
	- if we don't include $A[i]$, then the maximum value is equal to the value of $DP[i-1]$
		- if we do include $A[i]$, then the value in $i-1$ cannot be included
			- our maximum value is equal to $DP[i-2]+A[i]$

### Part d
>"Write the running time of the DP algorithm and the entry of the DP array the algorithm returns as its final answer"

- the algorithm will return $DP[n]$
	- this represents the maximum value of a valid subset of the entire array
- the runtime of the algorithm with be $O(n)$ as each entry in the DP table is calculated in constant time, and the table as total size $n$
