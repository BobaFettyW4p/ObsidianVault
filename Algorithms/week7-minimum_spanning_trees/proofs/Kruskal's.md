---
title: "Kruskal's"
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

### Claim
> the max height of the connected component returned by Kruskal's algorithm is $\leq log n$

### Property One
>A root of rank $k$ has $\geq 2^k$ nodes in its tree
#### Proof
- by induction on $k$
- **Base case:** $k=0$
	- $2^0 = 1
		- trivially proven
- **Inductive Hypothesis:** for all $m \leq k$, a root of rank $m$ has greater than or equal to $2^m$ nodes in its tree
- **Inductive Step:**
	- assume a root $r$ of rank $k$ has at least $2^k$ nodes in its tree, and so did $V$
		- this means that $r$ has at least $2*2^k = 2^{k+1}$ nodes in its true and the claim is proven

### Property Two
> If $n$ is equal to the total number of vertices, there are at most $\frac{n}{2^k}$ vertices of rank $k$

#### Proof
- we prove by contradiction
- suppose $k = log_2 n + 1$
	- since there are at most $\frac{n}{2^k}$ vertices of rank $k$:
		- $\frac{n}{2^k} = \frac{n}{2^{log_2 n + 1}} = \frac{n}{2^{log_2n * 2}} = \frac{n}{2n} = \frac{1}{2}$
		- this is a contradiction as there must be at least one vertex, and the claim is proven