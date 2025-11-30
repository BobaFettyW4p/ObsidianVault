---
title: Fermat's Little Theorem
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
  - [[week2-random_algorithms/pseudocode/Fermat's Test|Fermat's Test pseudocode]]
  - [[week2-random_algorithms/concepts/Rabin-Miller Primality Test|Rabin-Miller Primality Test]]
  - [[week2-random_algorithms/Week2 Dashboard|Week2 Dashboard]]
---

- *Theorem:*
	- if $N$ is prime, then for all integers $a$ such that $gcd(a,N) = 1, a \equiv 1 mod n$
		- if $a^{n-1} \not \equiv 1 mod n$, then $N$ must be composite
- is the inverse of Fermat's Little Theorem true?
	- No.
		- we cannot be sure that $N$ is prime
			- composite numbers that satisfy $F\ell t$ for all $a$ such that $1 \leq a \leq N-1$ but are composite are called Carmichael Numbers
				- smallest: $561 = 3 * 11 * 17$
				- there are 255 Carmichael numbers that have been found, but there are infinitely many
				- what is $N$ is not a Carmichael number?
					- Fermat liars satisfy $F \ell t$ for some value of $a$ but not all
