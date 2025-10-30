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