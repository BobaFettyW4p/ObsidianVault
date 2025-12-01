---
title: "Quiz 4"
type: problem
course: algorithms
week: 4
topic: data structures
tags:
  - algorithms
  - data-structures
  - week4
  - problem
related:
  - [[week4-data_structures/concepts/Binary Search Trees.md|Binary Search Trees]]
  - [[week4-data_structures/Week4 Dashboard|Week4 Dashboard]]
---

# Prompt
>"For this problem you may assume that a binary search tree (BST) node $x$ has fields $x.left$, $x.right$, $x.key$, and $x.p$ (for the parent node, or $NIL$ if $x$ is root). you may assume that the keys of the BST are distinct.
>*Definition* A node in a BST is a leaf if both its left and right child pointers are $NIL$
>Given the root node $x$ of a BST $T$, write pseudocode for a recursive algorithm to compute the number of leaves in $T$. your algorithm should run in $O(n)$ time, where $n$ is the number of nodes in $T$, and return an integer

## Example
- in the following example, there are 3 leaves (4, 11, 17)
- ```
		  10
		 /  \
		4    14
			/   \
		  11    17
  ```

### Solution
```
def leaves(T,x):
	if x == NIL:
		return NIL
	if x.left == NIL and x.right == NIL:
		return 1
	return leaves(x.left) + leaves(x.right)
```

#### Runtime
- this algorithm performs a constant amount of work in each call, and performs $n$ total calls
	- this gives us the desired runtime of $O(n)$
