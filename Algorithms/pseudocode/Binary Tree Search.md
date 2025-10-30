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