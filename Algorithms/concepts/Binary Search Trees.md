- maintains a complete ordering of data
	- rooted binary tree
	- each node $x$ has:
		- $key[x]$ - the data field
		- in addition, has at least 3 pointers per node:
			- $left[x]$ - points to the left child
			- $right[x]$ - points to the right child
			- $parent[x]$ - points to the parent node
		- $root[T]$ points to the root node of tree $T$

```text
		41
		/ \
	   20  65
	   / \   \
	  11  29   70
	      /
	     26
```
- height of the tree: length (in terms of # of edges) of the largest simple downward path from the root to a leaf

### Binary Search Tree Property
> " For any node $x$, for all nodes $y$ in left subtree of x, $key[y] \leq key [x]$"
> "For all nodes $y$ in right subtree of $x$, $key[y] \geq key[x]$"




