---
title: Recurrences
type: concept
course: algorithms
week: 1
topic: divide and conquer
tags:
  - algorithms
  - divide-and-conquer
  - week1
  - concept
related:
  - [[week1-divide_and_conquer/concepts/Recursion Trees|Recursion Trees]]
  - [[week1-divide_and_conquer/concepts/Master Theorem|Master Theorem]]
  - [[week1-divide_and_conquer/Week1 Dashboard|Week1 Dashboard]]
---

- closed form
	- nor having an algorithm express in terms of itself
- Recurrence: $T(n) = 2 T(n/2) + \Theta(n)$
	- each problem spawns two subproblems, each consisting of half the problem set, each instance performs O(n) work
	- merge sort
- Methods of solving recurrences:
	- Iteration method/recursion trees
	- Substitution method
	- Master theorem
