---
title: Sorting Problem
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
  - [[week1-divide_and_conquer/pseudocode/Insertion Sort|Insertion Sort pseudocode]]
  - [[week1-divide_and_conquer/pseudocode/Merge Sort|Merge Sort pseudocode]]
  - [[week1-divide_and_conquer/concepts/Merge Sort|Merge Sort]]
  - [[week1-divide_and_conquer/Week1 Dashboard|Week1 Dashboard]]
---

- given a list of $n$ objects, rearrange them in [in|de]creasing order
- *Input*: Array $A[1..n]$ of $n$ real numbers
- *Output:*  Permutation $A'$ of $A$ such that $A'[1] \leq A'[2] \leq \dots \leq A'[n]$
- Space complexity
	- "in place", i.e. the input array is sorted without an intermediary
		- constant amount of external storage (equal to the size of the original array)
