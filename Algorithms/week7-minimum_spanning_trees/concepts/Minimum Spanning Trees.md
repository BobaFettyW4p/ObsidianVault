---
title: "Minimum Spanning Trees"
type: concept
course: algorithms
week: 7
topic: minimum spanning trees
tags:
  - algorithms
  - minimum-spanning-trees
  - week7
  - concept
---

- *Input:* a connected undirected graph $G = (V,E)$ with a weight function $w: E -> \mathbb{R}$
	- no constraint on the weights
- *Output:* a spanning tree with minimum weight
	- $T = (V,E'), E' \subset E$

### Example
```mermaid
graph TD;

    a -- "16" --- b
    a -- "5" ---- c

    b -- "32" --- d
    c -- "8" ---- d

    b -- "17" --- e
    c -- "7" ---- f

    d -- "5" ---- g

    e -- "3" ---- g
    e -- "9" ---- g
    g -- "11" --- f
    e -- "12" --- f



```





#### Approaches
- look all the weights and select the minimum weight
	- use this edge in the spanning tree
- proceed to the next and repeat until a spanning tree that connects to all edges is yielded

```mermaid
flowchart TD

    a -->|5| c
    a -->|16| b

    c -->|3| g
    e -->|3| g

    d -->|5| g
    c -->|7| f

```
### Greedy Algorithms
- this is an example of a greedy approach to generate an MST
	- always picks the best option at the time
		- never backtracks to consider other options
- the *greedy choice property*
	- locally optimal choices lead to a globally optimal solution