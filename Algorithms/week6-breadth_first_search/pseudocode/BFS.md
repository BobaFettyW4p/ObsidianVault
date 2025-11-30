---
title: "BFS"
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
def BFS(G,s): //G is in adjacency list format
	for each vertex u in V:
		color[u] = WHITE
		d[u] = \infty
		parent[u] = NIL
	color[s] = GRAY
	d[s] = 0
	initialize empty queue Q
	Enqueue(Q,s)
	while Q != 0:
		u = Dequeue(Q) // remove u from Q
		for each v in Adj.[v]:
			if color[v] == WHITE:
				color[v] = GRAY
				d[v] = d[u] + 1
				parent[v] = u
				Enqueue(Q,v)
		color[u] = BLACK
```

```
def Print-Path(G,s,v):
	if v == s:
		print s
	else if parent[v] == NIL:
		print "no path"
	else: 
		Print-Path(G,s,parent[v])
	print(v)
```


### Runtime Analysis
- each vertex is added to the queue at most once $O(V)$
- each adjacency list is examined at most once
	- $\Sigma_u | Adj.[u] | = |E|$
- this gives us a runtime of $O(V) + O(E) = O(V+E)$