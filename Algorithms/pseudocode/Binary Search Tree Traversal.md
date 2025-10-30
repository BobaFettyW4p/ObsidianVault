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
