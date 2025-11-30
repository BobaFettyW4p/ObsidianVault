---
title: "Approaches to find maximum flow"
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

consider the following graph

```mermaid
flowchart LR

    s((s))
    u((u))
    v((v))
    t((t))

    s -->|5/20| u
    u -->|10/10| t
    u -->|5/30| v
    s -->|3/10| v
    v -->|8/20| t

```


this is a valid flow, but it's not optimal

### Greedy approach
- find an $s -> t$ directed path in $G$
	- always choose the maximum capacity edge
- find the minimum capacity of edges on the path
	- $c(p) = m \{c(u,v)\}: (u,v)$ where ($u,v$) is an edge on the path
- assign $f(e) = c(p)$ for all edges on the path
- repeat until no $s -> t$ path remains in $G$