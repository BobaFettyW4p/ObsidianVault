---
title: "Binary Tree Successor"
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
def Tree-Successor(x):
	if right[x] != NIL:
		return Tree-Minimum(right[x])
	y = parent[x]
	while y != NIL and x = right[y]:
		x = y
		y = parent[x]
	return y
```

### Runtime
- $O(h)$ where $h$ is the height of the tree