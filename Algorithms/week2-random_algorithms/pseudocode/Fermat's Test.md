---
title: Fermat's Test Pseudocode
type: pseudocode
course: algorithms
week: 2
topic: random algorithms
tags:
  - algorithms
  - random-algorithms
  - week2
  - pseudocode
related:
  - [[week2-random_algorithms/concepts/Fermat's Little Theorem|Fermat's Little Theorem]]
  - [[week2-random_algorithms/concepts/Rabin-Miller Primality Test|Rabin-Miller Primality Test]]
  - [[week2-random_algorithms/pseudocode/Trial division|Trial Division pseudocode]]
  - [[week2-random_algorithms/Week2 Dashboard|Week2 Dashboard]]
---

```
def Fermat's Test(N):
	to test if N is prime
	pick a in {2 .. N-1} at random
	if gcd(a,N) != 1:
		return "N is composite"
	else if a^{n-1} !\equiv 1 mod n:
		return "N is composite"
	else:
		return "N may be prime"
```

### Runtime
- calculating a random number is done in constant time
- calculating a gcd is $O(log N)$ in expectation by Euclid's algorithm
- Calculating equivalence in Fermat's Theorem is $O(logN)$ by repeated squaring
- total time: $O(log N)$
	- polynomial in input size (length $n = log N$)
