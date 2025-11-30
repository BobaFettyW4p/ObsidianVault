---
title: "Longest Common Subsequence"
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
  - [[week3-dynamic_programming/problems/Longest Common Subsequence.md|Longest Common Subsequence problem]]
---

```
def LCS-Length(X_1 .. X_m, Y_1 .. Y_n):
	if m=0 or n=0:
		return 0
	else if X_m = Y_n:
		return 1+ LCS-Length(X_1 .. X_m-1, Y_1 .. Y_n-1)
	else:
		return max(LCS-Length(X_1..X_m,Y_1..Y_n-1), LCS-Length(X_1..X_m-1,Y_1..Y_n))
```
