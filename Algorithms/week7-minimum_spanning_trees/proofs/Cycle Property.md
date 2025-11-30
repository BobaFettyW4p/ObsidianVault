---
title: "Cycle Property"
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
- let $G=(V,E)$ be a connected undirected weighted graph
	- suppose there is a cycle
		- let $e =(u,v)$ be a maximum weight edge on some cycle of $G$
		- there must be an MST of $G$ that does not include $e$

### Proof
- let $T$ be an MST of $G$
	- let $C$ be a cycle of $G$, and $e =(u,v)$ be an edge within $C$
- let us consider two separate cases:
	- case 1: $e =(u,v)$ is not in $T$
		- in this case, the theorem is proven
	- case 2: suppose $e \in T$
		- in this case, removing $e$ from $T$ would disconnect $T$ into 2 separate subtrees
		- consider the cut $S, V-S$, each of which constitutes one of these two subtrees
		- let $S$ be one side of the cut
			- since $e=(u,v)$ is on the cycle $C$, there is a path from $u$ to $v$ on the cycle $C$
			- some other edge in $C$, $e'$ has exactly one endpoint in $S$ and one endpoint in $V-S$
			- since $e$ is a maximum weight edge on $C$, $w(e') \leq w(e)$
				- replacing $e$ in $T$ with $e'$ will yield a valid tree with a lower weight than $T$ and therefore the theorem is proven