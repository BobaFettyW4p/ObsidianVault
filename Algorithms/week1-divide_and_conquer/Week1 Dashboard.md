---
title: Week1 Dashboard
type: dashboard
course: algorithms
week: 1
topic: divide and conquer
tags:
  - algorithms
  - divide-and-conquer
  - week1
  - dashboard
related:
  - [[week1-divide_and_conquer/concepts/Recurrences|Recurrences]]
  - [[week1-divide_and_conquer/concepts/Merge Sort|Merge Sort]]
  - [[week1-divide_and_conquer/pseudocode/Insertion Sort|Insertion Sort pseudocode]]
---

- Use these Dataview blocks to browse Week 1 notes by type.

```dataview
TABLE type, topic, file.link AS Note
FROM "week1-divide_and_conquer"
WHERE week = 1 AND course = "algorithms"
SORT type ASC, file.name ASC
```

```dataview
TABLE file.link AS Concept, topic
FROM "week1-divide_and_conquer"
WHERE type = "concept" AND week = 1
SORT file.name ASC
```

```dataview
TABLE file.link AS Pseudocode, topic
FROM "week1-divide_and_conquer"
WHERE type = "pseudocode" AND week = 1
SORT file.name ASC
```

```dataview
TABLE file.link AS Proof, topic
FROM "week1-divide_and_conquer"
WHERE type = "proof" AND week = 1
SORT file.name ASC
```
