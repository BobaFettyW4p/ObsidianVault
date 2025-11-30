---
title: "Min-Heap Decrease"
type: pseudocode
course: algorithms
week: 4
topic: data structures
tags:
  - algorithms
  - data-structures
  - week4
  - pseudocode
related:
  - [[week4-data_structures/concepts/Data Structures.md|Data Structures]]
---

```
MIN-HEAP-DECREASE(A,i,key):
	if A[i] \leq key:
		return n
	while i > 1 and A[PARENT(i)] > key:
		A[i] = A[PARENT(i)] //swap values
		i = PARENT(i)
	A[i] = key
```

##### Runtime
- again, in a worst case scenario, we must traverse the height of the tree
	- $O(log\ n)$