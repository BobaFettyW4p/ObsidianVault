---
title: "Knapsack problem theorem"
type: proof
course: algorithms
week: 3
topic: dynamic programming
tags:
  - algorithms
  - dynamic-programming
  - week3
  - proof
related:
  - [[week3-dynamic_programming/concepts/Dynamic Programming.md|Dynamic Programming]]
---

- *Theorem:* a restricted version of the Knapsack problem can be solved in $O(nW)$ steps

- **Idea:** instead of solving one problem, solve the entire table
	- consider a table, where each square is a subproblem of the knapsack problem with different inputs
	- let $M[i,j]$ be the maximum value for $[i,j]$
		- $M[i,j] = max S\subset \Sigma_{k \in S} ^{\{1,\dots,i\}} v_k$ such that $\Sigma_{k \in S} w_k < W$
			- $\Sigma_{k \in S} w_k \leq j$
			- this is the "brain" of the D.P. solution