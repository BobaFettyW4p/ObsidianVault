---
title: Rabin-Miller Overview
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
  - [[week2-random_algorithms/concepts/Rabin-Miller Primality Test|Rabin-Miller Primality Test]]
  - [[week2-random_algorithms/pseudocode/Fermat's Test|Fermat's Test pseudocode]]
  - [[week2-random_algorithms/Week2 Dashboard|Week2 Dashboard]]
---

- *Goal:* produce an algorithm that determines primality without running in exponential time
	- polynomial time preferred
- Monte Carlo version of this never incorrectly classifies primes
	- correctly classifies composites with $p \geq 0.75$
	- Time complexity: $O(log_2 N)$ operations
		- polynomial time
	- most popular and widely used algorithm for primality testing
		- old, but state of the art
