---
title: "Topological Sort 1"
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

```
def Topological-Sort(G):
	L !=0
	DFS(G)
	when vertex finishes, add it to L
	return reversed L
```

### Runtime Analysis
- $\texttt{DFS}$ takes $\Theta(V+E)$ time
- reversing a list of length $V$ will take $\Theta(V)$ time
- $\Theta(V+E) + \Theta(V) = \Theta(V+E)$