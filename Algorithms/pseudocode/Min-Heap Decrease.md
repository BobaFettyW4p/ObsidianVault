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