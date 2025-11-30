---
title: "Bellman-Ford Alg"
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
---

```
Bellman-Force(G,w,s):
	Initialize-Single-Source(G,s)
	for i=1 to |G.V| - 1:
		for each edge (u,v) in G.E:
			Relax(u,v,w)
	for each edge (u,v) in G.E:
		if v.d > u,d + w(u,v):
			return False
	return True
```

### Runtime
This algorithm traverses every vertex, and for each vertex, traverses each edge and thus runs in $O(VE)$ time

