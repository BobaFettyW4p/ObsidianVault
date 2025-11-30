---
title: "Knapsack problems"
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

- there are $n$ items
	- the items themselves are not part of the input
	- each item has a weight and a value
	- let $w_1, \dots, w_n$ be the weight of each item
	- let $v_1, \dots, v_n$ be the weight of each item
- there is a total weight limit of the knapsack, $W$
###  Goal
- Find $S \subset \{1,2,\dots,n\}$
	- subject to the constraint $\Sigma_{k \in S} w_k = W$
	- maximize the value $\Sigma_{k \in S} v_k$
	- in plain language, find what collection of items that does not exceed the weight limit of the knapsack have the maximum combined value
- number of possible choices: $2^n$
	- grows exponentially
- this problem is considered to be *np-complete*
	- it cannot be solved in polynomial time
	- the problem can be restricted, which will allow it to be solved in polynomial time
		- e.g if the weights and weight limit are all positive integers