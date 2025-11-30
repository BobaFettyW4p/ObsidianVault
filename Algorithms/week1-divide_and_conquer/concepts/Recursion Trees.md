---
title: Recursion Trees
type: concept
course: algorithms
week: 1
topic: divide and conquer
tags:
  - algorithms
  - divide-and-conquer
  - week1
  - concept
related:
  - [[week1-divide_and_conquer/concepts/Recurrences|Recurrences]]
  - [[week1-divide_and_conquer/concepts/Master Theorem|Master Theorem]]
  - [[week1-divide_and_conquer/concepts/Merge Sort|Merge Sort]]
  - [[week1-divide_and_conquer/Week1 Dashboard|Week1 Dashboard]]
---

- Ex. $T(n) = 2T(n/2) + O(n)

```mermaid
flowchart TD

    A["Level 0\n1 problem of size n"]

    A --> B1["Level 1\n2 problems of size n/2"]
    A --> B2["Level 1\n2 problems of size n/2"]

    B1 --> C11["Level 2\nn/4"]
    B1 --> C12["Level 2\nn/4"]
    B2 --> C21["Level 2\nn/4"]
    B2 --> C22["Level 2\nn/4"]

    C11 --> D1["Level 3\nn/8"]
    C11 --> D2["Level 3\nn/8"]
    C12 --> D3["Level 3\nn/8"]
    C12 --> D4["Level 3\nn/8"]
    C21 --> D5["Level 3\nn/8"]
    C21 --> D6["Level 3\nn/8"]
    C22 --> D7["Level 3\nn/8"]
    C22 --> D8["Level 3\nn/8"]

    D1 --> E["Level i\n2^i subproblems of size n / 2^i"]
    D2 --> E
    D3 --> E
    D4 --> E
    D5 --> E
    D6 --> E
    D7 --> E
    D8 --> E
```

- Total work on level $i$:
	- $(2^i)*(\frac{n}{2^i}) = n$
	- this problem stops when we have subproblems of size 1
		- so $i = log_2 n$
	- $T(n) = \Sigma_{i=0} 2^i(\frac{n}{2^i}) = n \Sigma_{i=0}^{log_2 n} = n*log_2 n$
	- 
