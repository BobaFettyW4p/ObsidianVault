---
title: "Relaxation"
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

- for each vertex v in V, we maintain an attribute $v.d$, which is an upper bound on the weight of a shortest path from $s$ to $v$.
	- $v.d$ is a "shortest path estimate"
- the process of relaxing an edge $(u,v)$ consists of testing whether we can improve the shortest path to $v$ found so far by going through $u$, and if so, updating $v.d$ and $v.\pi$
![[Pasted image 20251110210414.png]]

- properties of relaxation:
![[Pasted image 20251110210607.png]]
