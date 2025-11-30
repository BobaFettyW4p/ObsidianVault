---
title: Week4 Dashboard
type: dashboard
course: algorithms
week: 4
topic: data structures
tags:
  - algorithms
  - data-structures
  - week4
  - dashboard
related:
  - [[week4-data_structures/concepts/Data Structures.md|Data Structures]]
  - [[week4-data_structures/concepts/Binary Search Trees.md|Binary Search Trees]]
  - [[week4-data_structures/concepts/Heap.md|Heaps]]
---

- Use these Dataview blocks to browse Week 4 notes by type.

```dataview
TABLE type, topic, file.link AS Note
FROM "week4-data_structures"
WHERE week = 4 AND course = "algorithms"
SORT type ASC, file.name ASC
```

```dataview
TABLE file.link AS Concept, topic
FROM "week4-data_structures"
WHERE type = "concept" AND week = 4
SORT file.name ASC
```

```dataview
TABLE file.link AS Pseudocode, topic
FROM "week4-data_structures"
WHERE type = "pseudocode" AND week = 4
SORT file.name ASC
```
