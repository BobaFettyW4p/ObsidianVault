---
title: "Linear programming"
type: concept
course: algorithms
week: 8
topic: network flow
tags:
  - algorithms
  - network-flow
  - week8
  - concept
related:
  - [[week8-network-flow/concepts/Network Flow.md|Network Flow]]
---

- a method to maximize an objective function subject to constraints

#### Example
- maximize $x_1 + x_2$ subject to the following constraints:
	- $x_1 + 2*x_2 \leq 1$
	- $2*x_1 + x_2 \leq 1$
	- $x_1 \geq 0, x_2 \geq 0$

- a *feasible solution* satisfies the objective function given the constraints
	- the *objective value* is the value of the objective function using the feasible solution
	- linear programming yields a range of feasible solution, an optimal value can be calculated within this range