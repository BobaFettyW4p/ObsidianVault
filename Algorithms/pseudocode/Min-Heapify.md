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