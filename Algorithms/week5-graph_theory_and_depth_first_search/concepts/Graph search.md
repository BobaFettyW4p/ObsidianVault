---
title: "Graph search"
type: concept
course: algorithms
week: 5
topic: graph theory and depth-first search
tags:
  - algorithms
  - graph-theory
  - week5
  - concept
related:
  - [[week5-graph_theory_and_depth_first_search/concepts/Graph Theory.md|Graph Theory]]
---

- given a graph $G = (V,E)$$ and a start vertex $s \in V$
	- can be either directed or undirected

#### Explore
- visit every vertex $v$ reachable from $s$
- $s$ is reachable from itself (1)
- if $u$ is reachable from $s$, and $v \in Adj.[u]$, then $v$ is reachable from $s$ (2)
- the only vertices reachable from $s$ are those provably so from (1) and (2)
	- if $u$ is reachable from $s$, then there is a path $s->v_1->v_2->\dots->v_k->u$ of length $k+1$
#### Traceback
-  if $v$ is discovered from $u$, then $u$ is the parent of $v$
	- the parent of the start vertex is $NIL$
	- this is expressed as $\Pi[v] = u$

#### Paths
- a *path* is a sequence of vertices $v_0, v_1, \dots, v_k \in V$ connected by an edge
	- the *length* is the number of edges in the path
	- a *simple path* is a path with no repeated vertices