---
title: "Top Down DP"
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

- classic DP works bottom up and incrementally
	- a series of subproblems are solved once
		- some of these may not be needed to solve the problem
- *Idea:* combine the good features of recursive solutiosn and bottom-up dynamic programming

### Memoization
- method that solves subproblems only as needed
	- still only solve them once
		- store the results in a table