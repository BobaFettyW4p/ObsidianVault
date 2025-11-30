---
title: "Network Flow"
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
  - [[week8-network-flow/concepts/Flow Networks.md|Flow Networks]]
  - [[week8-network-flow/concepts/Ford-Fulkerson Algorithm.md|Ford-Fulkerson Algorithm]]
  - [[week8-network-flow/Week8 Dashboard|Week8 Dashboard]]
---

- a category of graph optimization problems
- **Input:** a directed graph with non-negative edge weights
	- these weights are called capacities
	- edges are capable of carrying some measurable quantity
		- each edge has a given capacity, which limits the amount of quantity it can carry
- **Idea:** determine how much flow that can be sent across the network from a designated source vertex to a designated sink/target vertex
