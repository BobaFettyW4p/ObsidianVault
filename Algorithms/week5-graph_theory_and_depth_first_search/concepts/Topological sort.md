---
title: "Topological sort"
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

- *Input:* a directed acyclic graph $G=(V,E)$
- *Output:* a list of vertices $v_1,v_2,\dots,v_n$ such that all edges go from left to right
- also called "linearizing" or "flattening"

### Job Scheduling
- $V =$ a set of tasks
- $E =$ a set of dependencies
	- e.g. an edge ($u,v$) indicates that $u$ must be done before $v$
- the goal is to complete all tasks without violating dependencies
#### Example

```mermaid
graph LR;
A --> C;
B --> A;
B --> D;
C --> E;
C --> F;
D --> C;
```

##### DFS table
|       | A   | B   | C   | D   | E   | F   |
| ----- | --- | --- | --- | --- | --- | --- |
| d     | 1   | 9   | 2   | 10  | 3   | 5   |
| f     | 8   | 12  | 7   | 11  | 4   | 6   |
| $\Pi$ | NIL | NIL | A   | B   | C   | C   |
##### Topological Sort
```mermaid
graph LR;
A --> C;
B --> A;
B --> D;
C --> E;
C --> F;
D --> C;

%% enforce ordering
B --- D --- A --- C --- F --- E
```
