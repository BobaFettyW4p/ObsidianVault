---
title: "Binary Tree Min"
type: pseudocode
course: algorithms
week: 4
topic: data structures
tags:
  - algorithms
  - data-structures
  - week4
  - pseudocode
related:
  - [[week4-data_structures/concepts/Data Structures.md|Data Structures]]
---

```
Tree-Min(x):
	while left[x] is not NIL:
		x = left[x]
	return x
```
### Runtime
$O(h)$