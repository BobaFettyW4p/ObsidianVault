---
title: Rabin-Miller Primality Test
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
  - [[week2-random_algorithms/concepts/Rabin-Miller|Rabin-Miller Overview]]
  - [[week2-random_algorithms/pseudocode/Fermat's Test|Fermat's Test pseudocode]]
  - [[week2-random_algorithms/pseudocode/Trial division|Trial Division pseudocode]]
  - [[week2-random_algorithms/Week2 Dashboard|Week2 Dashboard]]
---

- Monte Carlo algorithm for primality testing
	- randomized test to see if an algorithm is probably prime
- turns trial divison into a randomized witness searching algorithm
#### Trial division
- for $a = 1,2,3 \dots \sqrt{n}$
	- if $a | n$, then $n$ is not prime
		- otherwise, $n$ must be prime
		- this is very costly!
			- must be a better alternative

#### Primality Testing
- *Definition:* a positive integer $i \geq 2$ is prime if the only positive integers that divide it are 1 and itself
- positive integers that aren't prime are called *composite*
- *Definition:* The unique prime factorization of a positive integer $n$ is a representation of $n$ as a product of prime numbers
	- $n = p_i * \dots * p_k$

#### How to test for primality
**Idea:** look for evidence that $n$ is not prime
	if found, say $n$ is composite
		otherwise, say it's prime
