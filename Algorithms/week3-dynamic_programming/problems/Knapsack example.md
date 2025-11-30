---
title: "Knapsack example"
type: problem
course: algorithms
week: 3
topic: dynamic programming
tags:
  - algorithms
  - dynamic-programming
  - week3
  - problem
related:
  - [[week3-dynamic_programming/concepts/Dynamic Programming.md|Dynamic Programming]]
---

| Item | Weight | Value |
| ---- | ------ | ----- |
| 1    | 1      | 6     |
| 2    | 2      | 2     |
| 3    | 3      | 3     |
- let us consider a max weight $W=5$

- the DP table will be built as follows:

| i   | 0   | 1   | 2   | 3   | 4   | 5   |
| --- | --- | --- | --- | --- | --- | --- |
| 0   | 0   | 0   | 0   | 0   | 0   | 0   |
| 1   | 0   | 6   | 6   | 6   | 6   | 6   |
| 2   | 0   | 6   | 10  | 16  | 16  | 16  |
| 3   | 0   | 6   | 10  | 16  | 18  | 22  |
- the solution to this problem is $m[5,3]$ as this represents the problem when all 3 items are considered to be added to the knapsack, and the max weight of the knapsack is $5$, which is the correct max weight

```
def optimal-knapsack(W, w[1..n], m[0..n,0..W]):
	k = 0 // size of list S of items in an optimal solution
	j = W
	for i=n to 1, decreasing:
		if m[i,j] > m[i-1,j]:
			k=k+1
			S[k] = i // include item i
			j = j - w[i]
	return S
```