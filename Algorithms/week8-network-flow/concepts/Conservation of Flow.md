---
title: "Conservation of Flow"
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

- for every $v \in V$, such that $v \neq s,t$, $\Sigma_{u \in V} f(u,v) = \Sigma f(v,u)$
	- i.e. for all vertices except the start and sink vertex, flow in is equal to flow out
- the value of flow $f$ is defined as $|f| = \Sigma_{v \in V} f(s,v)$
	- i.e. the sum of the flow is equal to the sum of flow out the start vertex, which is equal to the sum of flow into the sink vertex