---
title: "Kruskal's Algorithm - Draft"
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
---

```
def Kruskal's(G,m): //m: number of edges
	sort the edges by weight // w(e1) <= w(e2) <= ... <= w(em)
	initialize X as an empty list // will contain the spanning tree edges
	for i=1 to m:
		if ei does not create a cycle with the edges in X:
			add ei to X
	return X
```

### Complexity Analysis
- sorting the edges can be done in $O(E log E)$ time
	- as we know that $|E| \leq |V|^2$
		- $log|E| \leq 2*log|V| = O(log V)$
- comparing edge edge to the existing tree and seeing if it forms a cycle requires $O(V)$ union operations
- how long does it take to determine if adding the edge constitutes a cycle?
	- depens on the graph
		- for undirected graphs, we have to use DFS
			- $O(V+E)$
		- if it's a directed graph, we can store each edge in a hashmap, and confirm both edges have not been visied
			- $O(V)$
- $\therefore, O(E log V) + P(EV) + O(V) = O(VE)$
