---
title: Master Theorem
type: concept
course: algorithms
week: 1
topic: divide and conquer
tags:
  - algorithms
  - divide-and-conquer
  - week1
  - concept
related:
  - [[week1-divide_and_conquer/concepts/Recurrences|Recurrences]]
  - [[week1-divide_and_conquer/concepts/Recursion Trees|Recursion Trees]]
  - [[week1-divide_and_conquer/concepts/Merge Sort|Merge Sort]]
  - [[week1-divide_and_conquer/Week1 Dashboard|Week1 Dashboard]]
---

> "If $T(n) = aT\lceil(\frac{n}{b})\rceil + O(n^d)$ for some constants $a \geq 1, b > 1, d \geq 0$, then"

- $T(n) = O(n^d)$ if $d > log_b a$
- $T(n) = O(n^d log_2 n)$ if $d = log_b a$
- $T(n) = O(n^{log_b a})$ if $d < log_b a$

- what does this mean?
	- $a$ is the number of subproblems
	- $b$ is the size of the subproblems
	- $n^d$ is the total amount of work done in each subproblem

- *Example:* in merge sort, since each iteration splits into 2 subproblems, both of which operate on half the problem set, and which perform a constant amount of work
	- $a = 2, b = 2, d = 1$
	- $log_b a ? d$
	- $log_2 2 = 1 = 1$
	- $\therefore,$ this equates to the second case
		- $T(n) = O(nlog_2 n) = O(nlogn)$
