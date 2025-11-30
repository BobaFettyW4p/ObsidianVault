---
title: "DFS on Directed Graphs"
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

```mermaid
graph TD;
A --> D;
A-->B;
B-->C;
B-->D;
C-->A;
D-->C;
```

#### Adjacency List
a: b,d
b: c,d
c: a
d: c

#### DFS Table

|       | A   | B   | C   | D   |
| ----- | --- | --- | --- | --- |
| d     | 1   | 2   | 3   | 5   |
| f     | 8   | 7   | 4   | 6   |
| $\Pi$ | NIL | A   | B   | B   |

#### DFS Tree
```mermaid
graph TD;

A --> B;
B --> C;
B --> D;
```

- $A$ is the root of the search tree
	- all other vertices are its descendents
		- $A$ is an ancestor of $B,C,D$
			- $B$ also has descendents: $C,D$