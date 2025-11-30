---
title: "Graph Reduction"
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
  - [[week6-breadth_first_search/concepts/Graph Reduction.md|Graph Reduction]]
---

```
def Input(G,w,c,s): // G: Adj. list format
	for every u in G.V:
		add u_in to G'.V
		add u_out to G'.V
	for every u in G.V:
		for every v in G.Adj.[u]:
			add u_out to G'.Adj[u_in]
			w'(u_in,u_out) = c(u)
			add v_in to G'Adj.[u_out]
			w'(u_out, v_in) = w(u,v)
	Dijkstra's(G',w',s_in)
```

### Runtime
- $O(V+E)$ 
	- linear plus Dijkstra's