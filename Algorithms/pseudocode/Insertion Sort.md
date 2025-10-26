```
INSERTION-SORT(A,n) // sort A[1..n]
	for j=2 to n
		key = A[j] // insert A[j] into sorted sequence A[1..j-1]
		i = j-1
		while i>0 and A[i] > key:
			A[i+1] = A[i]
			i = i-1
		A[i+1] = key
```

## Time and Space Complexity
- in the worst case, $A[i] > key$ for every $i = j-1, \dots, 1$
	- $key = A[j]$
	- occurs in the case of a reverse sorted list
- $T(n) = \Sigma^n_{j=2} (j-1)$
- $1 + 2 + \dots + n-1 = \frac{n(n-1)}{2}$
	- this is quadratic
- best case:
	- occurs in the case of a list that is already correctly sorted
	- $T(n) = \Sigma^n_{j=2} 1 = n-1$
	- this is linear

## Proof
- We prove by a loop invariant
### Loop invariant
- At the start of each iteration of the for loop on line 1 of the `INSERTION-SORT` algorithm, keys originally in `A[1..j-1]` are in `A[1..j-1]` but in sorted order

#### Initialization
- Case $j=2$
	- $A[1..j-1] = A[1]$
	- As the sub-array only contains one element, it is trivially in sorted order
#### Maintenance
- Suppose sorted $j-1$ elements
	- this means that $A[1..j-1]$ is sorted
	- the loop will insert item $j$ at position $A[k]$ at the back of the array
	- If $A[k] \leq A[j] < A[k+1]$, the sorted order is maintained and the maintenance step is proven

#### Termination
- The loop will termination when $j-1$ traverses every valid value in $n$
- Substituting $n$ for $j-1$ in the Loop Invariant gives us $A[1..n]$ and the termination step is proven