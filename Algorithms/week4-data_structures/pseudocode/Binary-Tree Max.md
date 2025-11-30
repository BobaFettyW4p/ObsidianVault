---
title: "Binary-Tree Max"
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
Tree-Max(x):
	while right[x] is not nil:
		x = right[x]
	return x
```

### Runtime
$O(h)$
