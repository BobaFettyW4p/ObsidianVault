---
title: Types of Random Algorithms
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
  - [[week2-random_algorithms/concepts/Intro|Random Algorithms Overview]]
  - [[week2-random_algorithms/concepts/Randomized Sorting Algorithms|Randomized Sorting Algorithms]]
  - [[week2-random_algorithms/Week2 Dashboard|Week2 Dashboard]]
---

- there are two types of random algorithms:
#### Monte Carlo
- also known as Von Neuman
- Monte Carlo algorithms always run in the same time
- they always return the correct output with high probability
	- may be incorrect
- *Example:* primality testing
### Las Vegas
- Las Vegas algorithms always return the correct output
- they run in expected time
	- which is not always the same time
- *Example:* Quicksort (randomized version)
