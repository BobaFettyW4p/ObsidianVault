---
title: "Binary Tree Insert"
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
Tree-Insert(T,z):
	# insert key v into BST T
	# insert new node z into BST with key[z] = v
	y = NIL
	X = root[T]
	while x is not NIL:
		y = x
		if key[z] < key[x]:
			x = left[x]
		else:
			x = right[x]
	p[z] = y
	if y = NIL:
		root[T] = x // T was empty
	else if key[z] < key[y]:
		left[y] = z
	else:
		right[y] = z
```

### Runtime
$O(h)$
