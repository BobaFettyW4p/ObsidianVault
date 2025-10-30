```code
In-OrderTreeWalk(x)
if x \neq NIL:
	In-OrderTreeWalk(left[x])
	print(key[x])
	In-OrderTreeWalk(right[x])
```

### Runtime
$O(n)$