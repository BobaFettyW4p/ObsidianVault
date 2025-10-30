```
MIN-HEAP-INSERT(A, key):
	heapsize[A] = heapsize[A]+1
	i = heapsize[A]
	while i>1 and A[PARENT(i)] > key:
		A[i] = A[PARENT(i)]
		i = PARENT(i)
	A[i] = key
```

##### Runtime
- the total number of value exchanges is, at most, equal to the height of the tree
	- $\therefore,$ $O(log\ n)
