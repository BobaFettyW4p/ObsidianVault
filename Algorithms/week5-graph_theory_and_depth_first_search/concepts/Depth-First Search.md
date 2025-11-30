---
title: "Depth-First Search"
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

- a search method that starts deeper then explores shallower alternatives
- the most basic question answered:
> "What parts of a graph are reachable from a given vertex?"

### Edge Classification
- not all edges in $G$ will appears in the $DFS$ tree
	- edges that appear are called *tree edges*
	- edges that do not are called *non-tree edges*
- non-tree edges in undirected graphs are called *back edges*
	- so called because they lead back to edges already visited
- a *tree edge* ($u,v$) has vertex $color[v] = WHITE$ when explored for the first time
	- $d[u] < d[v] < f[v] < f[u]$
		- e.g. $u$ is discovered before $v$, and $v$ finishes before $u$ finishes
- for a *back edge* ($u,v$), $color[v] = GRAY$ when explored for the first time
	- $d[v] < d[u] < f[u] < f[v]$
		- e.g. $v$ is discovered before $u$, and $u$ completes before $v$

### Edge Classification on Directed Graphs
- 4 types of edges on directed graphs
	- *tree edges* are part of the DFS forest
		- they are related as *ancestors* and *descendents*
	- *back edges* lead to an ancestor in the DFS tree
	- *forward edges* lead to a nonchild descendent in the DFS tree
	- *cross edges* lead to neither an ancestor or a descendent

##### Discovery/Finishing times for edge (u,v)
- Tree edges and forward edges
	- $d[u] < d[v] < f[v] < f[u]$
- Back edges
	- $d[v] < d[u] < f[u] < f[v]$
- Cross edges
	- two different possibilities:
		- $v$ finished before $u$ started exploring its subtree
			- $d[v] < f[v] < d[u] < f[u]$
		- $u$ finished before $v$ started
			- $d[u] < f[u] < d[v] < f[v]$
