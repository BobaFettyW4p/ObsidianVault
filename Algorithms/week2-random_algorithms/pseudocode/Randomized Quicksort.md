---
title: Randomized Quicksort Pseudocode
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
  - [[week2-random_algorithms/pseudocode/Quicksort|Quicksort pseudocode]]
  - [[week2-random_algorithms/proofs/Randomized Quicksort|Randomized Quicksort proof]]
  - [[week2-random_algorithms/Week2 Dashboard|Week2 Dashboard]]
---

- we are able to introduce randomness by randomly choosing the pivot
	- partition input around a random element selected via random sampling
```
def Randomized-Partition(A,p,r):
	i = Random(p,r) // select integer i at random in a range from p to r
	exchange A[r], A[i]
	return Partition(A,p,r)
```

```
def Randomized-Quicksort(A,p,r):
if p<r:
	q = Randomized-Partition(A,p,r)
	Randomized-Quicksort(A,p,q-1)
	Randomized-Quicksort(A,q+1,r)
```

### Runtime analysis
