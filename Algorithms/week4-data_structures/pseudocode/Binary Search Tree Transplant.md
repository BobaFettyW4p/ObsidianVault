---
title: "Binary Search Tree Transplant"
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
def Transplant(T,u,v):
	if p[u] = NIL:
		root[T] = v
	else if u = left[p[u]] // u is the left child
		left[p[u]] = v
	else:
		right[p[u]] = v
	if v \neq NIL:
		p[v] = p[u]
```