---
title: "Residual graphs"
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

#### Edges in the residual graph
- forward edges
	- for each edge ($u,v$) in $G$ for which $f(u,v) < c(u,v)$
		- create edge ($u,v$) in $G_f$ and assign it capacity $c_f(u,v) = c(u,v) - f(u,v)$
			- this signifies we can increase flow to this edge
- backward edges
	- for each edges ($u,v$) in $G$ for which $f(u,v) > 0$
		- create edge ($u,v$) in $G_f$ and assign it capacity $c_f(u,v) = f(u,v)$
			- this signifies we can decrease existing flow

##### $G$
```mermaid
flowchart LR

    s((s))
    u((u))
    v((v))
    t((t))

    s -->|20| u
    u -->|10| t
    u -->|30| v
    s -->|10| v
    v -->|20| t

```
##### $G_f$
```mermaid
flowchart LR

    s((s))
    u((u))
    v((v))
    t((t))

    u -->|20| s
    s -->|10| v
    t -->|20| v
    u -->|10| v
    v -->|20| u
    u -->|10| t

```

- can push additional flow along the augmented path up $c(p) = min\{c_f(u,v), (u,v) of p\}$

- augmented flow in $G$
	- $f'(e) = f(e) + c_f(p)$
		- if $e$ is a forward edge
	- $f'(e) = f(e) - c_f(p)$
		- if $e$ is a backward edge

- consider graph $G_f'$
```mermaid
flowchart LR

s((s))
u((u))
v((v))
t((t))

u -->|20| s
v -->|10| s
u -->|20| v
v -->|10| u
t -->|10| u
t -->|20| v
```

- there is no longer an $s -> t$ path
	- *claim:* this graph represents a maximum flow