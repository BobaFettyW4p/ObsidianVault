---
title: "Rabin-Miller"
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
  - [[week2-random_algorithms/Week2 Dashboard.md|Week2 Dashboard note]]
  - [[week2-random_algorithms/concepts/Rabin-Miller.md|Rabin-Miller]]
---

```
def Rabin-Miller(N,k):
	check carmicahel numbers
	repeat k times
	Fermats-Test(N)
```

### Correctness
- $p(error) \geq 1 - \frac{1}{2^k}$

### Runtime
- $O(k log N)$
