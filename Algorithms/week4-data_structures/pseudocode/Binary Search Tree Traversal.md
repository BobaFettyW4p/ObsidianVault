---
title: "Binary Search Tree Traversal"
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
Tree-Successor(x):
	if right[x] \neq NIL:
		return Tree-Minimumum(right[x])
	y = parent[x]
	while y \neq NIL and x = right[y]:
		x = y
		y = parent[x]
	return y
```

### Runtime
$O(h)$
