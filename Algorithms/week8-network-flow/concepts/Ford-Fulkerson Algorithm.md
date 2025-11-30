---
title: "Ford-Fulkerson Algorithm"
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

- Given a flow network $G = (V,E)$, for each pair of vertices $u,v$ in $V$, the residual capacity $c_f(u,v)$ is defined as:
	- $G_f(u,v) = c(u,v) - f(u,v)$ if $(u,v) \in E$
	- $G_f(u,v) = f(u,v)$ if $(v,u) \in E$
	- $G_f(u,v) = 0$ otherwise (if ($u,v$) and ($v,u$) are not in $E$)
- Let $f$ be a flow on a network $G = (V,E)$
	- the residual graph $G_f = (V,E)$ is a graph with edges $E_f = \{(u,v) \in VxV: c_f(u,v) > 0\}$
		- i.e. $G_f$ has edge ($u,v$) wherever $c_f(u,v) > 0$
- an *augmented path* $p$ is a directed path from $s -> t$ in a residual graph

- *Idea:* if $f'$ is a flow on the residual graph, then $f + f'$ is a flow in $G$
