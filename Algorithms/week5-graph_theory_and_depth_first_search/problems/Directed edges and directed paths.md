# Prompt
- You are given a possibly disconnected directeg raph $G=(V,E)$ in adjacency list format and an edge $e =(u,v) \in E$.

### Part a
>Write pseudocode for an $O(V+E)$ time algorithm to determine whether $G$ has a cycle containing $e$. You algorithm must run in $O(V+E)$ time and return a boolean. Prove your algorithm is correct.

```
def TestCycle(G,e = (u,v)):
	for each vertex v in G.V:
		x.color = WHITE
	return DFS-VisitBool(G,v,u)
```

```
def DFS-VisitBool(G, start, target):
	start.color = GRAY
	for each vertex in G.Adj[start]:
		if vertex == target:
			return TRUE
		else if vertex.color == WHITE:
			DFS-VisitBool(G, vertex, target)
	start.color = BLACK
	return FALSE
```

##### Proof
- consider edge $e = (u,v)$
	- since our graph is directed, if there is a path from $v$ to $u$ in our graph, we know that a cycle must exist that includes the edge ($u,v$)
		- this cycle is from $v$ to $u$ along this path, followed by the edge ($u,v$)
	- when we call $\texttt{DFS-VisitBool}$ from vertex $v$, it will either find a path back to $u$, in which case there is  a cycle...
		- in this case True is returned
	- or it will not, in which case there is not, and False is returned
- As our algorithm operates as a slightly modified version of BFS with a constant amount of additional work performed per vertex and edge, it has the typical BFS runtime of $O(V+E)$

### Part b
>Write pseudocode for an $O(V+E)$ runtime algorithm to determine whether $e$ belongs to every cycle in $G$. Prove your algorithm is correct.

```
def EdgeInEveryCycle(G, e = (u,v)):
	G' = G-e
	for each vertex v in G'.V:
		color[v] = WHITE
	for each vertex v in G'.V:
		if color[v] == WHITE:
			if DF=CycleCheck(G',v):
				return FALSE
	return TRUE
```

```
def DFS-CycleCheck(G',v):
	color[v] = GRAY
	for each y in G'.Adj[v]:
		if color[y] == GRAY:
			return TRUE // if there's a back edge, this forrms a cycle
		if color[y] == WHITE:
			if DGS-CycleCheck(G',y):
				return TRUE
	color[v] = BLACK
	return False
```

##### Proof
- *Claim*: Edge $e$ is in every directed cycle of $G$ $\iff$ $G-e$ is acyclic
	- forward direction: suppose $e$ is in every cycle of $G$
		- if $G-e$ still had a cycle $G$, then that cycle would exist in $G$
			- this contradicts the core claim that $e$ is in every cycle of $G$
	- backward direction: suppose $G-e$ is acyclic if $e$ is in every edge in $G$
		- let $C$ represent any cycle in $G$
			- deleting edge $e$ in $G$ would destroy the cycle $C$
				- as $G-e$ is acyclic, every such cycle is removed this way

### Part c
> You are given a directed acyclic graph $G=(V,E)$ in adjacency list format and two vertices $s,t \in V$. Write pseudocode for an $O(V=E)$ time algorithm that outputs the number of different directed paths from $s$ to $t$ in $G$.

```
def NumDirectedPaths(G,s,t):
	DP = table of size G.V
	T = topological sort of $G$
	for v in G.V:
		DP[v] = 0
	DP[s] = 1
	for vertex in T:
		for (v,w) in G.Adj[v]:
			DP[w] = DP[w] + DP[v]
	return DP[t]
```

### Part c 2
>Prove your algorithm is correct

- To begin with, we define  the subproblem of our dynamic programming approach as follows:
	- $DP[v] =$ the number of distinct directed paths from $s$ to $v$
- Proof: we will prove by induction
	- **Base Case:** $DP[s]$
		- as there is only one path from $s$ to itself, there is exactly
	- **Inductive Hypothesis:**
		- Let $T = (v_1,v_2,\dots,v_n)$ be a topological ordering of $G$
			- assume that for some $k \geq 1$, for every vertex $v_i$ with $i<k$:
				- $DP[v_i] =$ the number of directed paths from $s$ to $v_i$
	- **Inductive Step:**
		- let $v$ be a vertex in $T$ such that $v = v_{n+1}$
		- because the graph $T$ is a DAG, and $T$ is a topological ordering of $T$, every edge ($u,v$) enters $v$ from a vertex to the left of $v$
			- every directed path from $s$ to $v$ must reach $v$ through exactly one of its in-neighbors $u$
				- the number of such paths is equal to exactly the sum of the number of paths into each predecessor
					- by the IH, these quantites match the values in the DP table stored for $DP[u]$
						- Therefore, the algorithm computes the correct value for $DP[v]$  and the inductive step is proven