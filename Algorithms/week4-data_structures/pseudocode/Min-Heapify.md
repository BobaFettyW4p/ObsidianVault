---
title: "Min-Heapify"
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
MIN-HEAPIFY(A,i,heapsize[A]):
//left and right subtrees of i are min heaps
//restore the heap at index i

if left[i] \leq heapsize[A] and A[left[i]] < A[i]:
	smallest = left(i)
else:
	smallest = i
if right[i] \leq heapsize[A] and A[right[i]] < A[smallest]:
	smallest = right(i)
if smallest \leq i:
	exchange A[i] and A[smallest]
MIN-HEAPIFY(A, smallest, heapsize[A])
```