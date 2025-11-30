---
title: "Bipartite matching proofs"
type: proof
course: algorithms
week: 8
topic: network flow
tags:
  - algorithms
  - network-flow
  - week8
  - proof
related:
  - [[week8-network-flow/concepts/Network Flow.md|Network Flow]]
---

### Theorem
- $|M| of = |f|, max flow on G'$

#### Proof
- let $f$ be the max flow on $G'$ and $u \in L$ be any vertex in $L$
- if $f(s,u) =1$ then there exists a vertex $v \in R$ such that $f(u,v) = 1$ by flow conservation
- by construction of $G'$, for every $v \in R$, there exists an edge ($v,t$) with $c(v,t) = 1$
- if $f(v,t) > 0$, then $f(v,t) = 1$ by capacity constraint
	- so the augmenting path $s -> u -> v -> t$ matched vertex $u$ to vertex $v$
		- since $c(s,u) = 1$, there does not exist a vertex $v'$ with $f(u,v') = 1$
			- otherwise, flow conservation is violated
			- $\therefore, $u$ is matched to $v$ only
		- $\therefore,$ the set of such edges($u,v$), $M = \{ (u,v) \in G': u \in L, v \in R, f(u,v) = 1\}$ must be a matching in $G$

### Theorem
- $|M|$ is maximum

### Proof
- prove by contradiction
	- let $M'$ be a maximum matching for $G$
		- $|M'| > M$
		- construct a corresponding flow $f'$ in $G'$ as follows:
			- for every edge ($u,v$) in $M'$ such that $f'(u,v) = 1$
				- by previous argument, $f'$ is a valid flow in $G'$
					- $|f'| = |M'| > |M| = |f|$
						- this is a contradiction, and $f$ was a max flow