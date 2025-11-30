---
title: Week5 Dashboard
type: dashboard
course: algorithms
week: 5
topic: graph theory and depth-first search
tags:
  - algorithms
  - graph-theory
  - week5
  - dashboard
related:
  - [[week5-graph_theory_and_depth_first_search/concepts/Graph Theory.md|Graph Theory]]
  - [[week5-graph_theory_and_depth_first_search/concepts/Depth-First Search.md|Depth-First Search]]
  - [[week5-graph_theory_and_depth_first_search/concepts/Topological sort.md|Topological Sort]]
---

- Use these Dataview blocks to browse Week 5 notes by type.

```dataview
TABLE type, topic, file.link AS Note
FROM "week5-graph_theory_and_depth_first_search"
WHERE week = 5 AND course = "algorithms"
SORT type ASC, file.name ASC
```

```dataview
TABLE file.link AS Concept, topic
FROM "week5-graph_theory_and_depth_first_search"
WHERE type = "concept" AND week = 5
SORT file.name ASC
```

```dataview
TABLE file.link AS Pseudocode, topic
FROM "week5-graph_theory_and_depth_first_search"
WHERE type = "pseudocode" AND week = 5
SORT file.name ASC
```

```dataview
TABLE file.link AS Proof, topic
FROM "week5-graph_theory_and_depth_first_search"
WHERE type = "proof" AND week = 5
SORT file.name ASC
```
