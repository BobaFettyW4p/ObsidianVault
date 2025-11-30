---
title: "Graph Reduction"
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
  - [[week6-breadth_first_search/pseudocode/Graph Reduction.md|Graph Reduction pseudocode]]
---

- way of transforming the difficulty of one problem you don't know how to solve into one you do
	- consider a graph $G$:
		- with edge weights $\{w(e): e \in E\}$
		- and vertex costs: $\{c(v): c \in V\}$
		- the cost of a path is defined as the sum of its edge weights plus the sum of its vertex costs on the path
- *Input:*  directed graph $G=(V,E)$ with non-negative edge weight and non-negative vertex costs, a starting vertex $s$
- *Output:* array $dist[]$ such that for every vertex $v \in V$, $dist[v]=$ the cost of the fastest path from $s$ to $v$

### Solving
- Dijkstra's algorithm is not able to account for the vertex weights, but is effective for graphs with only edge weights
- **Idea:** is it possible to modify the input graph so Dijkstra's algorithm is able to be used?
	- convert each vertex to a $vertex_{in}$ and $vertex_{out}$ path with an edge connecting them equal to the vertex weight
		- each edge into the vertex enters the $vertex_{in}$ vertex, all edges out of the vertex leave via the $vertex_{out}$ vertex