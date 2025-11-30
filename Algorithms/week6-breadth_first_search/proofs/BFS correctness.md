---
title: "BFS correctness"
type: proof
course: algorithms
week: 6
topic: breadth-first search and shortest paths
tags:
  - algorithms
  - breadth-first-search
  - week6
  - proof
related:
  - [[week6-breadth_first_search/concepts/Breadth-First Search (BFS).md|Breadth-First Search (BFS)]]
---

- *Claim:* after BFS, $d[v] = \delta(s,v)$
- **To prove:** $d[v]$ is correctly computed for every $v$ in $V$
### Proof
- assume some vertex $v$ receives an incorrect value
	- $\delta(s,v) \neq d[v]$
	- assume $v$ is the first such vertex for which this is true
- by definition, $v \neq s$, as if this were true, $d[v] = 0$, and this is correctly set by the algorithm
- let $u$ be the vertex immediately preceding $v$ on a shortest path from $s$ to $v$
	- as such, $\delta(s,v) = \delta(s,u) + 1$
		- since $d[u]$ is correctly set, $d[u] = \delta(s,u)$ and $\delta(s,v) = d[u] + 1$
			- this means that $d[v] > \delta(s,v) = d[u] + 1$
				- let this be claim 1

*Claim:* when the algorithm dequeues $u$ from $Q$

### Proof
- At this time, $v$ is either white, gray, or black
	- if $v$ is white, then $d[v] = d[u] + 1$, which contradicts the claim 1
	- if $v$ is black, then $v$ was already removed from $Q$
		- this means that $d[v] \leq d[u]$, which contradicts claim 1
	- if $v$ is gray, then $v$ was colored gray upon dequeuing some other vertex $z$, which was dequeued earlier than $u$, so $d[z] \leq d[u]
		- This is a contradiction of claim 1 and so it's disproven