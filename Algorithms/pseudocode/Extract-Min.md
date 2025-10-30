```
HEAP-EXTRACT-MIN(A):
	min = A[1]
	A[1] = A[heapsize[A]]
	heapsize[A] = heapsize[A] - 1
	MIN-HEAPIFY(a,1,heapsize[A]) //repair the heap
	return min
```