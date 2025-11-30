---
title: "Longest Common Subsequence"
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
  - [[week3-dynamic_programming/pseudocode/Longest Common Subsequence.md|Longest Common Subsequence pseudocode]]
---

- *Problem*: given two sequences, find a longest subsequence common to both
	- $X=X[1..m], Y=Y[1..n]$
	- Note: find "a", not find "the"
		- there may be more than one subsequence with the same length, any sequence would be considered an optimal solution to the problem
- **Definition:** a sequence $Z = Z_1,Z_2, \dots, Z_k$ is a subsequence of $X=X1 \dots X_m$ if there is a sequence of integers $i_1, \dots i_k$ where $1 \leq i_1 \leq \dots \leq i_k \leq m$ such that $Z_j = X_{ij}$ for all $j=1,2,\dots,k$


#### Brute Force Approach
- for every subsequence of $X$, check if it is also a subsequence of $Y$
	- track the lengths and return the largest one found
	- this is guaranteed to work, but is slow
		- Worst case runtime: $\Theta(n*2^m)$
			- there are $2^m$ sequences, and each check takes $n$ time
			- exponential time
- *key idea:* the LCS of $X=X_1 \dots X_m$ and $Y = Y_1 \dots Y_n$ can be expressed as the LCS of smaller sequences
	- If $X_m = Y_n$, set the last symbol of the LCS to this symbol, and then find the LCS of $X_1 \dots X_{m-1}$ and $Y_1 \dots Y_n-1$
	- If $X_m \neq Y_n$, then the LCS of $X,Y$ is either the LCS of $X_1 \dots X_{m-1}$ and $Y_1 \dots Y_{n}$ or the LCS of $X_1 \dots X_m$ and $Y_1 \dots Y_{n-1}$

#### Dynamic Programming Solution
- Subproblem: lengths of different unique LCS's
- 
