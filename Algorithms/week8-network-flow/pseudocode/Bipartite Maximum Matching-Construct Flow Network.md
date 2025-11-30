---
title: "Bipartite Maximum Matching-Construct Flow Network"
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

- *Input:* a bipartite graph $G = (L\ u\ R, E)$
- *Output:* size $|M|$ of maximum matching of $G$
	- solve by reducing to max flow

### Procedure
- direct all edges from $G$ from $L$ to $R$
- add new vertices $s$ and $t$ such that $V' = V\ u\ \{s,t\}$
- create $|V|$ new directed edges from $s$ to each vertex $u$ in $L$ from each vetex $v \in R$ to $t$

```
def Construct-Flow-Network(G'):
	create (G',s,t,c):
	for each vertex u in G.V:
		add u to G'.V
	add s,t to G'.V
	for each vertex u in L:
		add u to G'.Adj[s]
		c(s,u) = 1
	for each vertex v in R:
		add t to G'.Adj[v]
		c(v,t) = 1
	for each vertex u in L:
		for each vertex v in G.Adj[u]:
			add v to G'.Adj[u]
			c(u,v) = 1
	Ford-Fulkerson(G',s,t,c)
	return |f| = \Sigma_{v \in V} f(s,v)
```

### Runtime analysis
- $O(V+E) + O(|f| * E)$
	- constructing the alternate graph plus Ford-Fulkerson