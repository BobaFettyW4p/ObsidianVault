---
title: Quicksort Pseudocode
type: pseudocode
course: algorithms
week: 2
topic: random algorithms
tags:
  - algorithms
  - random-algorithms
  - week2
  - pseudocode
related:
  - [[week2-random_algorithms/concepts/Quicksort|Quicksort (Randomized Perspective)]]
  - [[week2-random_algorithms/pseudocode/Randomized Quicksort|Randomized Quicksort pseudocode]]
  - [[week2-random_algorithms/proofs/Randomized Quicksort|Randomized Quicksort proof]]
  - [[week2-random_algorithms/Week2 Dashboard|Week2 Dashboard]]
---

```
def Quicksort(A,p,r): // A[p..r]
	if p < r:
		q = Partition(A,p,r)
		Quicksort(A,p,q-1)
		Quicksort(A,q+1,r)
```

```
def Partition(A,p,r): // A[p..r]
	x = A[r] // select pivot
	i = p-1
	for j=p to r-1:
		if A[j] = x:
			i = i+1
			exchange A[i], A[j]
	exchange A[i+1], A[r]
	return i+1
```

### Runtime

-  $T(n)$ depends on $q$ at each recursive call
	- $T(n) = O(n) + T(q-1) + T(n-1)$
- **Best Case**
	- occurs when the pivot always divides the array evenlt
	- $T(n) \leq 2T(\frac{n}{2}) + O(n)$
		- this is the same as merge sort
	- $T(n) = O(n log n)$
- **Worst Case**
	- $A[1] = 1, A[2] = 2, \dots, A[n] = n$
	- split
	- $(n-1) + (n-2) + \dots + 2 + 1 = \frac{n(n-1)}{2}$
	- $= O(n^2)$
- **Average case**
	- occurs on most input arrays
		- pretty close to best case
		- we declare a pivot "good" if it lies within the 25th - 75th percentile of a sorted array
	- $T(n) \leq T(n/4) + T(3n/4) + O(n)$
		- $T(n) = O(n log n)$
	- more generally, for $0 < a,b < 1$, where $a+b = 1$
		- $T(n) = T(an) + T(bn) + O(n)$
			- $T(n) = O(n logn)$
