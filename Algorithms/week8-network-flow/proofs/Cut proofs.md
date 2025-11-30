---
title: "Cut proofs"
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

### Claim
> $f(s) \leq c(S) by capacity constraint

#### Theorem
> for any flow network $G,|f| = c(S,T)$ for some $f$ and some cut $(S,T)$, the max flow is equal to the min cut
- the following are equivalent
	- $f$ is a max flow in $G$ (1)
	- $G_f$ has no augmenting path (2)
	- $|f| = c(S,T)$ for some cut $c(S,T)$ (3)

#### Proof
- 1 proves 2 by contraposition
	- if $G_f$ has an augmented path $p$, then it is possible to add flow from $s$ to get $f+c_f(p)$
		- $|f| + c_f(p) > |f|$
			- $\therefore,$ $f$ is not a maximum flow
- 2 can prove 3 as follows:
	- suppose $G_f$ has no augmenting path
		- Define $S = \{s\} union \{v\}
			- there exists a path from $s$ to $v$ in $G_f$ with $c_f(p) > 0$
				- $\therefore,$ ($S,T$) is a cut
	- for each vertex $u$ in $S$, $v \in T$, $f(u,v) = c(u,v)$
		- if $(u,v) \in E$, $f(u,v) = c(u,v)$; otherwise flow is residual
			- $c_f(u,v) = c(u,v) - f(u,v) > 0$
			- $\therefore, (u,v) \in c_f$, and $v \in S$
		- if $(v,u) \in E, f(u,v) = 0$
			- otherwise, $c_f(u,v) = f(u,v) > 0$
				- this forces $(u,v) \in G_f, v \in S$
		- if $(u,v), (v,u) \not \in G, f(u,v) = 0 = f(v,u)$
		- $f(S,T) = \Sigma_{u \in S} \Sigma_{v \in T} f(u,v) - \Sigma_{v \in T} \Sigma_{u \in S} f(v,u)$
		- $f(S,T) = \Sigma_{u \in S} \Sigma_{v \in T} c(u,v) - 0 = \Sigma \Sigma c(u,v) = c(S,T)$
	- since $|f| = f(S,T), |f| = c(S,T)$