---
title: "Bipartite Matching"
type: concept
course: algorithms
week: 8
topic: network flow
tags:
  - algorithms
  - network-flow
  - week8
  - concept
related:
  - [[week8-network-flow/concepts/Network Flow.md|Network Flow]]
---

- *Definition:* an undirected graph $G=(V,E)$ is bipartite if $V$ can partitioned into two sets, $L$ and $R$ such that $V = L\ u\ R$ and for all $e = (u,v)$ in $E$, $u \in L$, $v \in R$
- *Definition:* a matching $M$ in an undirected graph $G=(V,E)$ is a sub set of edges $M \subset E$ such that no two edges in $M$ share an endpoint
	- a matching is *maximum* if for any matching $M'$ of $G$, $|M'| \leq |M|$
