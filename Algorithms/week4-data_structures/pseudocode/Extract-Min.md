---
title: "Extract-Min"
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
HEAP-EXTRACT-MIN(A):
	min = A[1]
	A[1] = A[heapsize[A]]
	heapsize[A] = heapsize[A] - 1
	MIN-HEAPIFY(a,1,heapsize[A]) //repair the heap
	return min
```