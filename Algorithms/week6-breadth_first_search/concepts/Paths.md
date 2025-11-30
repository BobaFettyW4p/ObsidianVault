---
title: "Paths"
type: concept
course: algorithms
week: 6
topic: breadth-first search and shortest paths
tags:
  - algorithms
  - breadth-first-search
  - week6
  - concept
related:
  - [[week6-breadth_first_search/concepts/Breadth-First Search (BFS).md|Breadth-First Search (BFS)]]
---

- a path from $u->v$ in a graph $G=(V,E)$ is a sequence of adjacent vertices $u=v+0, \dots, v_k = v$ in $V$
	- the length of the path is the number of edges in the path
- a path is *simple* if there are no repeated vertices

### Example

```mermaid
graph TD;
a --- b;
a --- c;
b --- c;
c --- d;
b --- d;
```
- ($a,b,c,d$) is a path of length 3 from $a$ to $d$
- ($a,b,d$) is a path of length 2 from $a$ to $d$

- the *distance* form $u$ to $v$, $dist(u,v) = \delta(u,v)$ is the length of the shortest path from $u$ to $v$
	- ($a,b,d$) and ($a,c,d$) are both shortest paths from $a$ to $d$