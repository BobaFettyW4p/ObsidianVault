---
title: Random Algorithms Overview
type: concept
course: algorithms
week: 2
topic: random algorithms
tags:
  - algorithms
  - random-algorithms
  - week2
  - concept
related:
  - [[week2-random_algorithms/concepts/Types of random algorithms|Types of Random Algorithms]]
  - [[week2-random_algorithms/concepts/Randomized Sorting Algorithms|Randomized Sorting Algorithms]]
  - [[week2-random_algorithms/Week2 Dashboard|Week2 Dashboard]]
---

- *Definition*: a *random algorithm* is an algorithm that generates a random number $r$ from a range $\{0, \dots, \mathbb{R}\}$ and makes decisions or computations based on the value of $r$
	- the same input can return different values on different executions
- randomization is based on the steps the algorithm takes, not the input
	- advantage: algorithm runtime is no longer based on the input
		- prevents *adversarial algorithm input*
