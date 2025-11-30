---
title: Randomized Quicksort Proof
type: proof
course: algorithms
week: 2
topic: random algorithms
tags:
  - algorithms
  - random-algorithms
  - week2
  - proof
related:
  - [[week2-random_algorithms/pseudocode/Randomized Quicksort|Randomized Quicksort pseudocode]]
  - [[week2-random_algorithms/pseudocode/Quicksort|Quicksort pseudocode]]
  - [[week2-random_algorithms/concepts/Quicksort|Quicksort (Randomized Perspective)]]
  - [[week2-random_algorithms/Week2 Dashboard|Week2 Dashboard]]
---

- *Lemma:* For randomized quicksort, the total number of comparisons is $\leq 2n ln n$
- **Proof**
	- Input: $A[1..n]$
	- Let $S_1, S_2, \dots, S_n$ be the sorted order of $A$
		- $S_1 \leq S_2 \leq \dots \leq S_n$
	- for a pair $S_i,S_j$, they are compared at most once in the algorithm
		- this comparison occurs when either $S_i$ or $S_j$ is selected as the pivot
	- for $i < j$, let $R_{ij}$ be a random variable such that $R_{ij} = 1$ if $S_i,S_j$ are compared, and 0 otherwise
	- Let $R$ equal the total number of comparisons
		- $R = \Sigma_{i=1}^{n-1} \Sigma_{j=i+1}^n R_{ij}$
			- our goal is determine $E[R]$, the expected value
		- $E[R] = E[\Sigma_i \Sigma_j R_{ij} ] = \Sigma_{i=1}^{n-1} \Sigma_{j = i+1}^{n} E[R_{ij}]$
		- $E[R_{ij}] = 1*p(R_{ij = 1}) + 0*p(R_{ij} = 0)$
		- $E[R_{ij}] = p(R_{ij} = 1)$
	- when is $R_{ij} = 1$?
		- when $S_i$ or $S_j$ is selected as the pivot value
			- they must be selected before they are split by some other pivot
				- when this happens, they get placed in different subarrays and are never compared
	- $p(R_{ij}) = \frac{2}{j-i+1} = E[R_{ij}]$
	- $E[R_{ij}] = \Sigma_{i=1}^{n-1} \Sigma_{j=i+1}^{n} \frac{2}{j-i+1} = 2*\Sigma_{i=1}^{n-1} \Sigma_{j=i+1}^{n} \frac{1}{j-i+1}$
	- $E[R_{ij}] = 2 * \Sigma_{i=1}^{n-1} (1/2 + 1/3 + \dots + 1/n-i+1)$
	- $1 + 1/2 + 1/3 + 1/4 + \dots + 1/n$ is a geometric sequence that is $\leq 1 + ln n$
	- Therefore, $E[R_{ij}] = 2n(1 + ln n) = 2n + 2n ln n$
	- this gives us the average runtime of $O(n log n)$
