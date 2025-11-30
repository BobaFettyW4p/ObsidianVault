---
title: "Topological sort correctness"
type: proof
course: algorithms
week: 5
topic: graph theory and depth-first search
tags:
  - algorithms
  - graph-theory
  - week5
  - proof
related:
  - [[week5-graph_theory_and_depth_first_search/concepts/Graph Theory.md|Graph Theory]]
---

- **To prove:** for any edge ($u,v$) in $E$, $u$ is ordered before $v$
	- i.e. that $f[v] < f[u]$

### Proof
- When the graph is explored via DFS, what are the colors of vertices $u$ and $v$ in edge ($u,v$)?
	- $u$ is discovered first, so it will be gray. The color of $v$ will vary
		- is $v$ gray?
			- This is not possible because ($u,v$) is not a backedge
		- is $v$ white?
			- If so, $d[u] < d[v] < f[v] < f[u]$
				- and therefore, the statement is proven
		- if $v$ black?
			- If so, then $v$ has already finished, so $f[v] < f[u]$
				- and therefore, the statement is proven
		- these are all of the valid colors for an edges, and so the claim is proven