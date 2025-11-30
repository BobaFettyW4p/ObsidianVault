---
title: "Dijkstra's Algorithm"
type: concept
course: algorithms
week: 6
topic: breadth-first search and shortest paths
tags:
  - algorithms
  - breadth-first-search
  - week6
  - concept
related:
  - [[week6-breadth_first_search/concepts/Breadth-First Search (BFS).md|Breadth-First Search (BFS)]]
  - [[week6-breadth_first_search/pseudocode/Dijkstra's Algorithm.md|Dijkstra's Algorithm pseudocode]]
---

*Input:* $G = (V,E), w, s$
- $G$ is in adjacency list format
- $w$ is a weight function $w: E -> \mathbb{R}$
	- $(\forall e \in E)(w(e) \geq 0)$
- $s \in V$

*Output:* 
- $d[v]$: distance = minimum weight of shortest paths from $s$ to $v4
- $\Pi[v]$: predecessor/parent on shortest path tree

### Example
```mermaid
graph TD;
A -->|4| B;
B -->|2| F;
A -->|2| C;
A -->|1| D;
D -->|2| E;
C -->|1| B;
C -->|4| F;
C -->|3| E;
E -->|1| F;
D -->|1| C;
```

##### Adjacency List
a: b,c,d
b: f
c: b,e,f
d: c,e
e: f

##### Tables

| d   | A   | B   | C   | D   | E   | F   |
| --- | --- | --- | --- | --- | --- | --- |
|     | 0   | 4   | 2   | 1   | 3   | 4   |
|     |     |     |     |     |     |     |

| $\Pi$ | A   | B   | C   | D   | E   | F   |
| ----- | --- | --- | --- | --- | --- | --- |
|       | NIL | C   | A   | A   | D   | E   |

##### BFS Tree
```mermaid
graph TD;

A --> C;
C --> B;
A --> D;
D --> E;
E --> F;
```

### Limitations
- Dijkstra's algorithm does not handle negative weights properly

#### Example
```mermaid
graph TD;

s -->|2| v1;
s -->|3| v2;
v2 -->|-2| v1;
v1 -->|2| v3;
```

##### Adjacency List
s: v1, v2
v1: v3
v2: v1

|      | s   | v1  | v2  | v3  |
| ---- | --- | --- | --- | --- |
| d    | 0   | 1   | 3   | 4   |
| $Pi$ | NIL | v2  | S   | V1  |
##### What's the issue?
- $\delta(s,v_3) = 3$
- $d[v_3] = 4$
	- Dijkstra's is not able to reliably account for negative paths