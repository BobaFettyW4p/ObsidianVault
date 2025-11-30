---
title: Week6 Dashboard
type: dashboard
course: algorithms
week: 6
topic: breadth-first search
tags:
  - algorithms
  - breadth-first-search
  - week6
  - dashboard
related:
  - [[week6-breadth_first_search/concepts/Breadth-First Search (BFS).md|Breadth-First Search]]
  - [[week6-breadth_first_search/concepts/Dijkstra's Algorithm.md|Dijkstra's Algorithm]]
  - [[week6-breadth_first_search/concepts/Bellman-Ford.md|Bellman-Ford Algorithm]]
---

- Use these Dataview blocks to browse Week 6 notes by type.

```dataview
TABLE type, topic, file.link AS Note
FROM "week6-breadth_first_search"
WHERE week = 6 AND course = "algorithms"
SORT type ASC, file.name ASC
```

```dataview
TABLE file.link AS Concept, topic
FROM "week6-breadth_first_search"
WHERE type = "concept" AND week = 6
SORT file.name ASC
```

```dataview
TABLE file.link AS Pseudocode, topic
FROM "week6-breadth_first_search"
WHERE type = "pseudocode" AND week = 6
SORT file.name ASC
```

```dataview
TABLE file.link AS Proof, topic
FROM "week6-breadth_first_search"
WHERE type = "proof" AND week = 6
SORT file.name ASC
```
