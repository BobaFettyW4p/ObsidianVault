```
Tree-Delete(T,z):
	if left[z] is NIL:
		Transplant(T,z,right[z])
	else if right[z] =  NIL:
		Transplant(T,z,left[z])
	else:
		y = Tree-Minimum(Right[z])
		if p[y] \neq z:
			Transplant(T,y,Right[y])
			right[z] = right[y]
			p[right[y]] = y
		Transplant(T,y,z)
		left[y] = left[z]
		p[left[y]] = y
```