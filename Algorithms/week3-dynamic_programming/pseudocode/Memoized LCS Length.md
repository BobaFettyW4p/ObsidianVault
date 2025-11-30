---
title: "Memoized LCS Length"
type: pseudocode
course: algorithms
week: 3
topic: dynamic programming
tags:
  - algorithms
  - dynamic-programming
  - week3
  - pseudocode
related:
  - [[week3-dynamic_programming/concepts/Dynamic Programming.md|Dynamic Programming]]
---

```
def Memoized-LCS-Length(X,Y):
	create new table C[0..m,0..n]
	for i=1 to m:
		c[i,0] = 0
	for j=1 to n:
		c[0,j] = 0
	for i=1 to m:
		for j=1 to n:
			c[i,j] = -1
	return Lookup-LCS(m,n)	
```

```
def Lookup-LCS(i,j):
	if C[i,j] >= 0:
		return c[i,j]
	if X[i] = Y[j]:
		C[i,j] = Lookup-LCS(i-1,j-1) + 1
	else:
		C[i,j] = max(Lookup-LCS(i-1,j), Lookup-LCS(i,j-1))
	return C[i,j]
```

### Runtime
- worse case: still $O(mn)$ in the event the entire table must be solved