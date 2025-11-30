---
title: "Knapsack problem"
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
def knapsack(n, w[1..n], v[1..n], W):
	for i=0 to n:
		m[i,0] = 0
	for j=1 to W:
		m[0,j] = 0
	for i=1 to n:
		for j=1 to W:
			if w_k > j:
				m[i,j] = m[i-1,j]
			else:
				m[i,j] = (*)
	retuyrn m[n,W]
```

#### Recurrence `(*)`
- for each item $i$
	- we can either do not take it for the knapsack, in which case $m[i,j] = m[i-1,j]$
	- or we do, in which case $m[i,j] = m[i-1,j-w[i]] + v[i]$
- this gives the following recurrence for this DP problem:
	- $m[i,j] = max(m[i-1,j], m[i-1,j-w[i]] + v[i])$
		- this recurrence has the following base cases:
			- $M[i,0] = 0$ (if the max weight is 0, no items can fit in the knapsack)
			- $M[0,j] = 0$ (if there are no items that can be placed in the knapsack)

### Runtime analysis
- $O(n) + O(W) + O(nW) = O(nW)$
