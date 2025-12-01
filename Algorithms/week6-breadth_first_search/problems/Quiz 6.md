---
title: "Quiz 6"
type: problem
course: algorithms
week: 6
topic: breadth-first search and shortest paths
tags:
  - algorithms
  - breadth-first-search
  - week6
  - problem
related:
  - [[week6-breadth_first_search/concepts/Dijkstra's Algorithm.md|Dijkstra's Algorithm]]
  - [[week6-breadth_first_search/Week6 Dashboard|Week6 Dashboard]]
---

# Prompt
>"Given the weighted directed graph $G$ below with source vertex $s$ and target vertex $t$. Run Dijkstra's algorithm on $G$ starting at $s$ to find the shortest path from $s$ to $t$

```mermaid
graph TD
s -->|3| a;
s -->|2| b;
a -->|-4| b;
b -->|2| t;
```

### Part a
>"What does Dijkstra's algorithm return? For each vertex in $G$, write the $d$ value computed by the algorithm

|       | s     | a   | b   | t   |
| ----- | ----- | --- | --- | --- |
| d     | 0     | 3   | 2   | 4   |
| $\Pi$ | $NIL$ | s   | s   | b   |
### Part b
>"What is the length of the shortest path from $s$ to $t$?

- the shortest path from $s$ to $t$ is ($s,a,b,t$), which has a total cost of $1$
