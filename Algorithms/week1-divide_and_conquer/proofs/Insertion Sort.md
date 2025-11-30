---
title: Insertion Sort Correctness
type: proof
course: algorithms
week: 1
topic: divide and conquer
tags:
  - algorithms
  - divide-and-conquer
  - week1
  - proof
related:
  - [[week1-divide_and_conquer/pseudocode/Insertion Sort|Insertion Sort pseudocode]]
  - [[week1-divide_and_conquer/concepts/Loop Invariant|Loop Invariant]]
  - [[week1-divide_and_conquer/concepts/Sorting problem|Sorting Problem]]
  - [[week1-divide_and_conquer/Week1 Dashboard|Week1 Dashboard]]
---

### Proof
We prove by a loop invariant

#### Loop Invariant
> "At the start of iteration $i$ of the for loop, keys origianlly in $A[1..i-1]$ are in $A[1..i-1]$ but in sorted order"

#### Initialization
- Case $j=2$
	- $A[1..j-1] = A[1]$
	- As the sub-array only contains one element, it is trivially in sorted order

#### Maintenance
- Suppose sorted $j-1$ elements
	- this means that $A[1..j-1]$ is sorted
	- the loop will insert item $j$ at position $A[k]$ at the back of the array
	- If $A[k] \leq A[j] < A[k+1]$, the sorted order is maintained and the maintenance step is proven
#### Termination
- The loop will termination when $j-1$ traverses every valid value in $n$
- Substituting $n$ for $j-1$ in the Loop Invariant gives us $A[1..n]$ and the termination step is proven
