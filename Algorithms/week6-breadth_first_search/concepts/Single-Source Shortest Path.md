---
title: "Single-Source Shortest Path"
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

- select a vertex $S$
	- the source
- return a list of distances $dist(S,x)$ for all vertices $x \in V$
	- we want to be able to do this in linear time
		- $O(V+E)$