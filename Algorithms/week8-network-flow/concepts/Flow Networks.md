---
title: "Flow Networks"
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

- *Definition:* a *flow network* is a directed graph $G = (V,E)$ with a source vertex $s \in V$, a sink vertex $t \in V$, and edge capacities $C: VxV -> `\mathbb{R}`$ satisfying a capacity constraint :
	- $0 \leq f(u,v) \leq c(u,v) \forall u,v \in V$
- *Definition*: a $flow on $G$ is a function $f: VxV -> \mathbb{R}$ satisfying a capacity constraint:
	- $0 \leq f(u,v) \leq c(u,v) \forall u,v \in V$


