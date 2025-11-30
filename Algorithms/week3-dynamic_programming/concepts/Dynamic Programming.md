---
title: "Dynamic Programming"
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
  - [[week3-dynamic_programming/concepts/Steps to develop a DP solution.md|Steps to develop a DP solution]]
  - [[week3-dynamic_programming/concepts/Top Down DP.md|Top Down DP]]
  - [[week3-dynamic_programming/Week3 Dashboard|Week3 Dashboard]]
---

- a method to efficiently solve problems with overlapping or repeated subproblems
	- solve each subproblem exactly once and store the result to use later

### Deriving a Recurrence
- relates the solution of the small problems to the solution of the big problem
	- note: if the number of distinct subproblems is exponential, dynamic programming generally doesn't shorten the amount of work
