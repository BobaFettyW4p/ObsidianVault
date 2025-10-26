```
MERGE-SORT(A,p,r)
if p < r:
	q = floor((p+r)/2) // divide the array in 2
	MERGE-SORT(A,p,q) // recursively sort the left half
	MERGE-SORT(A,q+1,r) //recursively sort the right half
	MERGE(A,p,q,r) // merge the 2 sorted subarrays
```

```
MERGE(A,p,q,r) // input: two sorted subarrays
n1 = q-p+1 // length of A[p..q]
n2 = r-q // length of A[q+1..r]
create arrays L[1..n1+1], R[1..n2+1]
for i=1 to n1 //copy elements in A[p..r] to L
	L[i] = A[p+i-1]
for j=1 to n2 //copy elements in A[p..q] to R
	R[i] = A[q+j]
L[n1+1] = infinity // sentinel value
R[n2+1] = infinity
i = 1 //merge arrays L and R back into A[p..r]
j = 1
for k=p to r
	if L[i] <= R[j]
		A[k] = L[i]
		i = i+1
	else 
		A[k] = R[j]
		j = j+1
```

- Initial call to sort an array of size $n$: MERGE-SORT(A,1,n)
- 
- applies divide and conquer paradigm to sorting
- Recurrence: $T(n) = 2 T(n/2) + \Theta(n)$
