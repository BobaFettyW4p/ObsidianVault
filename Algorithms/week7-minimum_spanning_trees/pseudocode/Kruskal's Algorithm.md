---
title: "Kruskal's Algorithm"
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
  - [[week7-minimum_spanning_trees/concepts/Kruskal's Algorithm.md|Kruskal's Algorithm]]
---

```
def Kruskal(G,w):
	X = 0 // edges will be added here
	for each vertex u in V:
		makeset(u)
	sort E by increasing weight
	for each edge (u,v) in E: // will be processed in increasing order of weight
		if find(u) != find(v):
			X = X union {(u,v)}
			union(u,v)
	return X
```

```
def makeset(X):
	parent(X) = X
	rank(X) = 0
```
- each vertex has a rank, which corresponds to the height of the tree hanging from that vertex

```
def find(u): // name of the tree containing x
	while x != parent[x]:
		x = parent[x]
	return x
```

```
def union(x,y):
	link(find(x),find(y))
```

```
def link(x,y):
	if rank(x) > rank(y):
		parent(y) = x
	else:
		parent(x) = y
	if rank(x) == rank(y):
		rank(y) += 1
```

### Complexity Analysis
- $|V|$ total makesetups - complexity $O(1)$
- 2|E| total finds - complexity $O(log V)$
- $|V-1|$ unions - $O(log V)$
- sorting the edges - $O(E log V)$
- total cost: $O(V) + O(E log V) + O(V) + O(E log V) = O(E log V)$