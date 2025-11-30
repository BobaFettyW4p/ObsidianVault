---
title: "Edmonds-Karp"
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

- very similar to Ford-Fulkerson, with one strict rule added:
	- when augmenting, always augment along the shortest $s-t$ path in the residual graph, found by BFS
		- completely changes the running time: $O(VE^2)$