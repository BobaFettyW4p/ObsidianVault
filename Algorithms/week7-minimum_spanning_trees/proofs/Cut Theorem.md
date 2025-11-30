---
title: "Cut Theorem"
type: proof
course: algorithms
week: 7
topic: minimum spanning trees
tags:
  - algorithms
  - minimum-spanning-trees
  - week7
  - proof
related:
  - [[week7-minimum_spanning_trees/concepts/Minimum Spanning Trees.md|Minimum Spanning Trees]]
---

### Theorem
- For any cut ($S, V-S$) in a connected undirected weighted graph $G=(V,E,w)$, any least weight edge $v$ crossing the cut is in some MST of $G$

### Proof
- let $T$ be an MST of $G$
	- if $e \in T$, it is proven
		- suppose it is not
	- since $T$ is a tree, there a unique path between any 2 vertices in $T$
		- since $T$ spans $G$, there is a unique path in $T$ between any $u$ and $v$ in $G.V$
	- since $u \in S$ and $v  \in V-S$, there must be some edge $e'$ crossing the cut
	- If $e =(u,v)$ is added to $T$, this path in addition to $e'$ form a cycle
	- If the edge $e'$ is removed, then $T' = T - e' u \{e\}$ is a spanning tree
	- $T' must have the same weight as $T$
	- $w(T') = w(T) + w(u,v) - w(x,y) \leq w(T)$
		- $w(T') \leq w(T)$
			- $\therefore, T'$ is an MST