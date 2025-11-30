---
title: "Ford-Fulkerson proofs"
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

### Does it terminate?

*Claim*: if capacities are integral, then the algorithm terminates

*Proof*:
- $|f'| > |f|$
	- as $|f'| = |f| + c_f(p)$
- $c_f(p)>0$ so the flow is always increasing by some amount
- $\therefore,$ $f$ is strictly increasing
- There is only finite capacity coming out of $s$ by the claim
	- $\Sigma_{u \in V} c(s,u)$
	- $c(S)$ by the capacity constraint
	- $f(s) \leq c(s)$
- $\therefore, c_f(p) \geq 1$

### When it terminates, is it correct?
*Claim:* assuming capacities are integral, does the algorithm terminate and return the maximum flow?



### Runtime complexity


