---
title: "Midterm Problem 4"
type: problem
course: algorithms
week: 3
topic: dynamic programming
tags:
  - algorithms
  - dynamic-programming
  - week3
  - problem
related:
  - [[week3-dynamic_programming/concepts/Dynamic Programming.md|Dynamic Programming]]
  - [[week3-dynamic_programming/Week3 Dashboard|Week3 Dashboard]]
---

## Prompt
> "A beautiful river crosses the country of Algorithmia. There are $N$ towns in the North bank of the river and $M$ towns in the South bank. The towns in the North bank and South bank are numbere $1..N$ and $1..M$ from West to East, respectively.
> You want to build bridges between towns in the North and South bank. A bridge connecting cities $i$ and $j$ is respresented by a pair$(i,j$) where $1 \leq i < N$ and $1 \leq j < M$. The bridges must satisfy the following constraints:
> Each town can be the endpoint of at most one bridge.
> Bridges cannot cross. In other words, if you build bridges ($i,j$) and ($i',j'$) such that $i < i'$, then $j < j'$
> The towns of Algorithmia have ratings, which are given in the arrays $U[1..n]$ and $V[1..M]$. The rating of the $i$th city in the North bank is $U[i]$ and the rating of the $j$th city in the South bank is $V[j]$.  Building a bridge ($i,j$) yields a value of $U[i] + V[j]$
> Using Dynamic Programming, compute the maximum possible sum of values of a set of non-crossing bridges, given arrays $U[1..N]$ and $V[1..M]$

### Part a
>"Define the subproblems that you will use and state the exact dimensions of your DP table"

- *Idea:* when bridges cannot cross, the structure is identical to computing an "alignment" between prefixes of the two sequences
	- let us consider only the first $i$ towns on the north bank and the first $j$ towns on the sourh bank. The optimal value among bridges connecting these towns forms a table
- We will define $DP[i,j]$ as equal to the maximum total value using towns $1..i$ on the north bank and $1..j$ on the south bank.
	- this table will have a size of $(N+1)*(M+1)$
		- we will include 0 as a valid value to represent a base case of no towns being available on a specific bank

### Part b
> "Write a recurrence expressing $DP[i,j]$ in terms of smaller subproblems and justify it"

- *Idea:* for $DP[i,j]$, we have three options:
	- do not use north town $i$
		- value is $DP[i-1,j]$
	- do not use south town $j$
		- value is $DP[i,j-1]$
	- use bridge ($i,j$)
		- this contributes $U[i]+V[j]$ to the total, and as bridges must not cross, previous choices must come from $DP[i-1,j-1]$
			- value is $DP[i-1,j-1]+(U[i]+V[j])$
- this gives us a final recurrence of:
	- $DP[i,j] = max(DP[i-1,j], DP[i,j-1], DP[i-1,j-1]+U[i]+V[j])$
		- this recurrence has two base cases as follows:
			- $DP[0,j] = DP[i,0] = 0$
				- these base cases account for the case where there are 0 towns on the north bank, or 0 towns on the south bank; in these cases, there can be no bridges constructed, and so the maximum value is 0

### Part c
>"Write pseudocode for your algorithm.

```
def MaxBridgeValue(U[1..N],V[1..M]):
	create table DP[0..N][0..M]
	for i=0 to N:
		DP[i][0] = 0
	for j=0 to M:
		DP[0][j] = 0
	for i=1 to N:
		for j=1 to M:
			DP[i][j] = max(DP[i-1][j],DP[i][j-1],DP[i-1][j-1]+U[i]+V[j])
	return DP[N][M]
```

### Part d
>"Analyze the running time of your DP algorithm"

- the DP table has $N*M$ total entries
	- each entry can be completed in $O(1)$ time
	- therefore, our total runtime is $O(NM)$
