---
title: Week7 Dashboard
type: dashboard
course: algorithms
week: 7
topic: minimum spanning trees
tags:
  - algorithms
  - minimum-spanning-trees
  - week7
  - dashboard
related:
  - [[week7-minimum_spanning_trees/concepts/Minimum Spanning Trees.md|Minimum Spanning Trees]]
  - [[week7-minimum_spanning_trees/concepts/Prim's Algorithm.md|Prim's Algorithm]]
  - [[week7-minimum_spanning_trees/concepts/Kruskal's Algorithm.md|Kruskal's Algorithm]]
---

- Use these Dataview blocks to browse Week 7 notes by type.

```dataview
TABLE type, topic, file.link AS Note
FROM "week7-minimum_spanning_trees"
WHERE week = 7 AND course = "algorithms"
SORT type ASC, file.name ASC
```

```dataview
TABLE file.link AS Concept, topic
FROM "week7-minimum_spanning_trees"
WHERE type = "concept" AND week = 7
SORT file.name ASC
```

```dataview
TABLE file.link AS Pseudocode, topic
FROM "week7-minimum_spanning_trees"
WHERE type = "pseudocode" AND week = 7
SORT file.name ASC
```

```dataview
TABLE file.link AS Proof, topic
FROM "week7-minimum_spanning_trees"
WHERE type = "proof" AND week = 7
SORT file.name ASC
```
