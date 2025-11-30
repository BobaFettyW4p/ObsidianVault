---
title: "Fibonacci Numbers"
type: pseudocode
course: algorithms
week: 3
topic: dynamic programming
tags:
  - algorithms
  - dynamic-programming
  - week3
  - pseudocode
related:
  - [[week3-dynamic_programming/concepts/Dynamic Programming.md|Dynamic Programming]]
  - [[week3-dynamic_programming/concepts/Fibonacci numbers.md|Fibonacci numbers]]
---

```
def Fibonacci(n): // input: non-negative integer n
	if n=0:
		return 0
	if n=1:
		return 1
	return Fibonacci(n-1) + Fibonacci(n-2)
```

### Runtime
- $T(n) =$ the number of computer steps needed to compute $F(n)$
- $T(n) < 2$ for $n \leq 1$
- $T(n) = T(n-1) + T(n-2) + 3$ for $n > 1$
	- this is very inefficient