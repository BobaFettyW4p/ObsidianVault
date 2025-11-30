---
title: "Graph Reduction correctness"
type: proof
course: algorithms
week: 6
topic: breadth-first search and shortest paths
tags:
  - algorithms
  - breadth-first-search
  - week6
  - proof
related:
  - [[week6-breadth_first_search/concepts/Breadth-First Search (BFS).md|Breadth-First Search (BFS)]]
---

- Path representation in $G'$
	- any path from $s_{in}$ to $v_{out}$ in $G'$ will alternately pass through $v_{in}$ and $v_{out}$ for vertices along the path
		- the edge ($v_{in},v_{out}$) ensures the vertex cost $C(v)$ is added exactly once for every vertex along the path
	- correspondence with the original path cost
		- in $G$
			- $p: s-> \dots -> u$ 
				- $\Sigma_{edges \in p} w(e) \neq \Sigma_{v \in p} w(v)$
		- in $G'$
			- $p': s_{in} -> s_{out} -> \dots -> v_{in} -> v_{out}$
				- $\Sigma w(e)  + \Sigma w(v)$
				- $w(e): (v_{out}, u_{in})$
				- $c(v): (v_{in}, v_{out})$