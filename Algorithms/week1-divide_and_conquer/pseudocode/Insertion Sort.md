---
title: Insertion Sort
type: pseudocode
course: algorithms
week: 1
topic: divide and conquer
tags:
  - algorithms
  - divide-and-conquer
  - week1
  - pseudocode
related:
  - [[week1-divide_and_conquer/concepts/Sorting problem|Sorting Problem]]
  - [[week1-divide_and_conquer/concepts/Loop Invariant|Loop Invariant]]
  - [[week1-divide_and_conquer/proofs/Insertion Sort|Insertion Sort correctness]]
  - [[week1-divide_and_conquer/Week1 Dashboard|Week1 Dashboard]]
---

```
INSERTION-SORT(A,n) // sort A[1..n]
	for j=2 to n
		key = A[j] // insert A[j] into sorted sequence A[1..j-1]
		i = j-1
		while i>0 and A[i] > key:
			A[i+1] = A[i]
			i = i-1
		A[i+1] = key
```

## Time and Space Complexity
- in the worst case, $A[i] > key$ for every $i = j-1, \dots, 1$
	- $key = A[j]$
	- occurs in the case of a reverse sorted list
- $T(n) = \Sigma^n_{j=2} (j-1)$
- $1 + 2 + \dots + n-1 = \frac{n(n-1)}{2}$
	- this is quadratic
- best case:
	- occurs in the case of a list that is already correctly sorted
	- $T(n) = \Sigma^n_{j=2} 1 = n-1$
	- this is linear
