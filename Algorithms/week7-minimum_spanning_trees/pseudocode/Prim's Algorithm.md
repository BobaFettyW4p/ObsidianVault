---
title: "Prim's Algorithm"
type: pseudocode
course: algorithms
week: 7
topic: minimum spanning trees
tags:
  - algorithms
  - minimum-spanning-trees
  - week7
  - pseudocode
related:
  - [[week7-minimum_spanning_trees/concepts/Minimum Spanning Trees.md|Minimum Spanning Trees]]
  - [[week7-minimum_spanning_trees/concepts/Prim's Algorithm.md|Prim's Algorithm]]
---

```
def Prim's(G,w,r): // G=(V,E) in Adj. list format, r is the start vertex
	for each u in V:
		key[u] = \infty
		parent[u] = NIL
		mark[u] = 0 // u is in Q
	key[r] = 0
	Q = V // ordered by key[u]
	while Q is not empty:
		u = Extract-Min(Q)
		mark[u] = 1
		for each v in Ad. list[u]
			if v in Q = mark[u] = 0 and w(u,v) < key[v]:
				parent[v] = u
				key[v] = w(u,v)
```

### Runtime complexity
- there are $V$ total inserts
	- $V$ extract-mins
		- and $E$ decrease keys
- the actual runtime will vary based on the implementation of the priority queue
	- an array will have a total runtime of$O(V^2)$
	- a min-heap will have $O(|V+E| log V) = O(E log V)$