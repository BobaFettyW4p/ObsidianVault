---
title: "Binary Tree Search"
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
Tree-Search(x,k):
#begin at node x (root of tree), search for node k
if x = NIL or k = key[x]:
	return x
if k < key[x]:
	return Tree-Search(left)
else
	return Tre-Search(right)
```

### Runtime
$O(h)$, where $h$ is the height of the tree