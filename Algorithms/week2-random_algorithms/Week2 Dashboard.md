---
title: Week2 Dashboard
type: dashboard
course: algorithms
week: 2
topic: random algorithms
tags:
  - algorithms
  - random-algorithms
  - week2
  - dashboard
related:
  - [[week2-random_algorithms/concepts/Intro|Random Algorithms Overview]]
  - [[week2-random_algorithms/concepts/Quicksort|Quicksort (Randomized Perspective)]]
  - [[week2-random_algorithms/concepts/Rabin-Miller Primality Test|Rabin-Miller Primality Test]]
---

- Use these Dataview blocks to browse Week 2 notes by type.

```dataview
TABLE type, topic, file.link AS Note
FROM "week2-random_algorithms"
WHERE week = 2 AND course = "algorithms"
SORT type ASC, file.name ASC
```

```dataview
TABLE file.link AS Concept, topic
FROM "week2-random_algorithms"
WHERE type = "concept" AND week = 2
SORT file.name ASC
```

```dataview
TABLE file.link AS Pseudocode, topic
FROM "week2-random_algorithms"
WHERE type = "pseudocode" AND week = 2
SORT file.name ASC
```

```dataview
TABLE file.link AS Proof, topic
FROM "week2-random_algorithms"
WHERE type = "proof" AND week = 2
SORT file.name ASC
```
