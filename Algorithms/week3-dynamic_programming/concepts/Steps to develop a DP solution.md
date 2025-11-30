---
title: "Steps to develop a DP solution"
type: concept
course: algorithms
week: 3
topic: dynamic programming
tags:
  - algorithms
  - dynamic-programming
  - week3
  - concept
related:
  - [[week3-dynamic_programming/concepts/Dynamic Programming.md|Dynamic Programming]]
---

- characterize the structure of an optimal solution
- define the recurrence to calculate the value of an optimal solution
- compute the value via the table
- construct/return the solution


### Two ways to implement a D.P. solution
- "top down"
	- memozation
	- write the procedure recursively
		- save the result of each subproblem
		- the procedure will check if it has solved this subproblem, and if so, return the value
- "bottom up"
	- solving a subproblem of size $n$ requires solving subproblems smaller than $n$
		- sort subproblems by size and solve the smaller ones first