---
title: "Dijkstra's Algorithm"
type: pseudocode
course: algorithms
week: 6
topic: breadth-first search and shortest paths
tags:
  - algorithms
  - breadth-first-search
  - week6
  - pseudocode
related:
  - [[week6-breadth_first_search/concepts/Breadth-First Search (BFS).md|Breadth-First Search (BFS)]]
  - [[week6-breadth_first_search/concepts/Dijkstra's Algorithm.md|Dijkstra's Algorithm]]
---

```
def Dijkstra(G,w,s):
	initialize-single-source(G,s)
	S = 0
	Q = V // keyed by d[v]
	while Q != 0:
		u = Extract-Min(Q)
		S = S union {u}
		for every vertex v in Adj.[u]:
			Relax(u,v,w)
```

```
Initialize-Single-Source(G,s):
	for each vertex $v$ in $G.V$:
		v.d = \infty
		v.pi = NIL
	s.d = 0
```

after initialization, $v.\pi = NIL$ for all $v \in V$, $s.d = 0$ and $v.d = \infty$ for all $v$ other than $s$

```
Relax(u,v,w):
	if v.d > u.d + w(u,v):
		v.d = u.d + w(u,v)
		v.pi = u
```

### Complexity
- depends on the type of data structure used for Q
	- needs to support the following operations:
		- $\texttt{Insert(Q,key)}$
		- $\texttt{Extract-Min(Q)}$
		- $\texttt{Decrease-Key(Q,v,new\_key)}$
		- an array will perform these operations in $O(1)$, $O(V)$, and $O(1)$ time
			- will overall make the runtime $O(V^2)$
		- a min-heap will perform these in $O(|V+E| log V )$
			- an array is slower unless the graph is dense