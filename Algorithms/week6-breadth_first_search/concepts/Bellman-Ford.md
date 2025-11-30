---
title: "Bellman-Ford"
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

- solves the single-source shortest-paths problem in the general case where weights may be negative
	- returns a boolean indicating whether there is a negative-weight cycle reachable from the source
		- if this exists, there is no shortest path
			- otherwise it produces the shortest path and its weights

![[Pasted image 20251110211234.png]]