---
title: "Ford-Fulkerson"
type: pseudocode
course: algorithms
week: 8
topic: network flow
tags:
  - algorithms
  - network-flow
  - week8
  - pseudocode
related:
  - [[week8-network-flow/concepts/Network Flow.md|Network Flow]]
---

```
def Ford-Fulkerson(G=(V,E),s,t,c):
	for each edge (u,v) in G.E:
		f(u,v) = 0
	while there exists an augmented path in G_f:
		find an augmented path p in G_f
		compute capacity c_f(p) of p
		augment the flow in G by c_f(p) along p in G
		for each edge (u,v) in p:
			if (u,v) in G.E:
				f(u,v) = f(u,v) + c_f(p)
			else:
				f(u,v) = f(u,v) - c_f(p)
	return f // |f| = \Sigma_{v \in V} f(s,u)
```

### Runtime Analysis
- each augmentation runs in $O(E)$ time
	- if capacities are integers in $[0,c]$, then there are at most $|F| \leq c$ augmentations
- Thus, Ford-Fulkerson is expected to run in $O(E*c) = O(|F|*E)$ time