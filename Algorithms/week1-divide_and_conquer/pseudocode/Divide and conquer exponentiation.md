---
title: Divide and Conquer Exponentiation
type: pseudocode
course: algorithms
week: 1
topic: divide and conquer
tags:
  - algorithms
  - divide-and-conquer
  - week1
  - pseudocode
related:
  - [[week1-divide_and_conquer/concepts/Recurrences|Recurrences]]
  - [[week1-divide_and_conquer/concepts/Master Theorem|Master Theorem]]
  - [[week1-divide_and_conquer/Week1 Dashboard|Week1 Dashboard]]
---

```
def Power(a,n):
	x = a
	for i=2 to n:
		x = x*a
	return x
```

### Runtime analysis
- this algorithm performs $n$ multiplications (simplification), so the runtime is O(n)
	- can this be done better?
- *Idea:*
	- $a^n = a^{\lfloor \frac{n}{2} \rfloor} * a^{\lceil \frac{n}{2} \rceil}$
	- once we compute the first factor, we can compute the second by using at most one additional multiplication
```
def FastPower(a,n):
	if n=1:
		return a
	else:
		x = FastPower(a, \lfloow n/2 rfloor)
		if n is even:
			return x*x
	else:
		return x*x*a
```

### Runtime Analysis
- Recurrence:
	- $T(n) \leq T(\lfloor \frac{n}{2} \rfloor) + 2$
	- by the master theorem:
		- $a = 1, b = 2, d = 0$
			- $log_b a ? d$
			- $log_2 1 = 0 = 0$
			- $T(n) = O(n^0 log_2 n) = O(log_2n)$
			- logarithmic time
