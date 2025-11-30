---
title: Trial Division Pseudocode
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
  - [[week2-random_algorithms/concepts/Rabin-Miller Primality Test|Rabin-Miller Primality Test]]
  - [[week2-random_algorithms/concepts/Fermat's Little Theorem|Fermat's Little Theorem]]
  - [[week2-random_algorithms/pseudocode/Fermat's Test|Fermat's Test pseudocode]]
  - [[week2-random_algorithms/Week2 Dashboard|Week2 Dashboard]]
---

```
def TrialDivision(n):
	for k=2 to \sqrt{n}:
		if k | n:
			return "n is composite"
	return "n is prime"
```

### Runtime
- $O(\sqrt{n})$ divisions in worst case
	- represents when the number is prime - less than ideal if you're trying to find a prime number!
- since $n$ is represented by $n = log_2 N$ binary bits so runnint time is $O(\sqrt{2^n} = O(2^{\frac{n}{2}}))$
	- exponential!
