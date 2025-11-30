---
title: Loop Invariant
type: concept
course: algorithms
week: 1
topic: divide and conquer
tags:
  - algorithms
  - divide-and-conquer
  - week1
  - concept
related:
  - [[week1-divide_and_conquer/pseudocode/Insertion Sort|Insertion Sort pseudocode]]
  - [[week1-divide_and_conquer/proofs/Insertion Sort|Insertion Sort correctness]]
  - [[week1-divide_and_conquer/Week1 Dashboard|Week1 Dashboard]]
---

- a statement that we use to prove the correctness of iterative algorithms that use loops
	- bears a passing resemblance to inductive proofs
- 3 steps to prove
	- Intiialization - proves the Loop Invariant is true initially prior to the 1st iteration
	- Maintenance - If the loop invariant is true prior to one iteration, it will remain true after the current one and prior to the next
	- Termination - The loop invariant is true when the algorithm terminates
