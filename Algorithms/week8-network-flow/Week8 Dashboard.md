---
title: Week8 Dashboard
type: dashboard
course: algorithms
week: 8
topic: network flow
tags:
  - algorithms
  - network-flow
  - week8
  - dashboard
related:
  - [[week8-network-flow/concepts/Network Flow.md|Network Flow]]
  - [[week8-network-flow/concepts/Ford-Fulkerson Algorithm.md|Ford-Fulkerson Algorithm]]
  - [[week8-network-flow/concepts/Bipartite Matching.md|Bipartite Matching]]
---

- Use these Dataview blocks to browse Week 8 notes by type.

```dataview
TABLE type, topic, file.link AS Note
FROM "week8-network-flow"
WHERE week = 8 AND course = "algorithms"
SORT type ASC, file.name ASC
```

```dataview
TABLE file.link AS Concept, topic
FROM "week8-network-flow"
WHERE type = "concept" AND week = 8
SORT file.name ASC
```

```dataview
TABLE file.link AS Pseudocode, topic
FROM "week8-network-flow"
WHERE type = "pseudocode" AND week = 8
SORT file.name ASC
```

```dataview
TABLE file.link AS Proof, topic
FROM "week8-network-flow"
WHERE type = "proof" AND week = 8
SORT file.name ASC
```
