---
title: "Quiz 5"
type: problem
course: algorithms
week: 5
topic: graph theory and depth-first search
tags:
  - algorithms
  - graph-theory
  - week5
  - problem
related:
  - [[week5-graph_theory_and_depth_first_search/concepts/Depth-First Search.md|Depth-First Search]]
  - [[week5-graph_theory_and_depth_first_search/Week5 Dashboard|Week5 Dashboard]]
---

# Prompt
>"Run the DFS-based topological sort algorithm from class on the following graph. Whenever you have a choice of vertices, pick the one that is alphabetically first. Begin with vertex A.

```mermaid
graph LR

A --> B;
A --> D;
B --> E;
E --> D;
E --> F;
F --> C;
C --> G;
A --> C;
F --> G;
```

### Part a
>List the discovery times $d[v]$ and finishing times $f[v]$ of each of the vertices

|        | A   | B   | C   | D   | E   | F   | G   |
| ------ | --- | --- | --- | --- | --- | --- | --- |
| $d[v]$ | 1   | 2   | 7   | 4   | 3   | 6   | 8   |
| $f[v]$ | 14  | 13  | 10  | 5   | 12  | 11  | 9   |
### Part b
>"What topological ordering is found by the algorithm?

```mermaid
graph LR

A --> B;
A --> D;
B --> E;
E --> D;
E --> F;
F --> C;
C --> G;
A --> C;
F --> G;

%% enforce ordering
A --- B --- E ---- F --- C --- G --- D
```
### Part c
>"How many valid topological orderings does this graph have? Justify briefly.

- as $D$ has dependencies, but no node is dependent on $D$, $D$ can be placed at any point after it's predecessors $A,B,E$
	- there are 4 valid spots for $D$, so there are $4$ valid topological sorts for this graph
