---
title: "Fibonacci Numbers Dynamic"
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
---

```
def FibonacciDynamic(n):
	if n=0:
		return 0
	create array f[0..n]
	f[0] = 0
	f[1] = 1
	for i=2 to n:
		f[i] = f[i-1] + f[i-2]
	return f[i]
```

### Runtime
- $n-1$ total additions perform
	- $O(n)$ runtime
### Space Complexity
- creates an array of size $n$ to store values
	- $O(n) space complexity