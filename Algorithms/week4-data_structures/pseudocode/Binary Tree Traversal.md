---
title: "Binary Tree Traversal"
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

```code
In-OrderTreeWalk(x)
if x \neq NIL:
	In-OrderTreeWalk(left[x])
	print(key[x])
	In-OrderTreeWalk(right[x])
```

### Runtime
$O(n)$