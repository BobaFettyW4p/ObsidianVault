---
title: "Longest Common Subsequence DP Solution"
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
def LCS-Length(X,Y,m,n):
	create table C[0..m,0..n]
	for i=1 to m:
		C[i,0] = 0
	for j=0 to n:
		C[0,j] = 0
	for i=1 to mL
		for j=1 to n:
			if X[i] = Y[j]:
				C[i,j] = C[i-1,j-1] + 1
			else if C[i-1,j] > C[i,j-1]:
				C[i,j] = C[i-1,j]
			else:
				C[i-1,j] = max(C[i-1,j], C[i,j-1]) 
	return C[m,n]
```

### Example
- X = ABA
- Y = BAB

| i/j   | 0   | 1 (B) | 2 (A) | 3 (B) |
| ----- | --- | ----- | ----- | ----- |
| 0     | 0   | 0     | 0     | 0     |
| 1 (A) | 0   | 0     | 1     | 1     |
| 2 (B) | 0   | 1     | 1     | 2     |
| 3 (A) | 0   | 1     | 2     | 2     |
### Complexity
- a table of size $m*n$ is created
	- each entry can be filled in in $O(1)$ time
		- $O(mn)$ time complexity with $O(mn)$ space complexity