---
title: "Directed Acyclic Graphs"
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

- Let $G$ be a directed graph
	- $G$ is acyclic if it contains no directed cycles

#### Example
```mermaid
graph TD;
A --> C;
C --> E;
B --> A;
D --> C;
B --> D;
C --> F;
```

- how can we determine if a directed graph is acyclic?
	- DFS - if there are no back edges, it's acyclic
		- $\therefore, \Theta(V+E)$