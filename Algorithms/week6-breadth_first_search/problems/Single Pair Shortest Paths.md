# Prompt
>Paul Erdos was a prolific Hungarian matehmatician who collaborated with over 500 other researchers. The Erdos number of a researched mesasures the collaborative distance between Paul Erdos and that researcher. This problemw ill consider a generalization of the Erdos number. Given two researchers, we want to find the collaborative distance between them

### Part a
>Write pseudocode for a $O(V+E)$ time algorithm that takes $G$ and $s,t \in V$ as inputs (where $s$ and $t$ each are researchers) and returns the collaborative distance between researchers $s$ and $t$

```
def ResearcherPublishingDistance(G=(V,E), s, t):
	if s == t:
		return 0
		
	for each vertex v in G.V:
		color[v] = WHITE
		d[v] = \infty
	color[s] = GRAY
	d[s] = 0
	create empty queue Q
	Enqueue(Q,s)
	
	while Q is not empty:
		u = Dequeue(Q)
		for each v in Adj[u]:
			if color[v] = WHITE:
				color[v] = GRAY
				d[v] = d[u] + 1
				if v = t:
					return d[v]
				Enqueue(Q)
		color[u] = BLACK
	return NIL // s and t are disconnected
	
```

### Part b
>"Now suppose that no researcher has coauthored with more than $d$ other researchers"
>Prove that your algorithm only visits $O(d^k)$ vertices, where $k$ is the collaborative distance of $s$ and $t$

- by definition, level one of our algorithm will traverse at most $d$ vertices.
	- the next level will at most have each of those $d$ vertices traverse $d$ additional vertices
		- the total number of vertices traversed at this level is $d^2$ at most
			- at level $i$, our algorithm will traverse at most, $d^i$ vertices
				- in addition, our algorithm will terminate as soon as the target researcher si found, so we know our algorithm will only traverse $k$ levels deep
					- this will mean we traverse $d^k$ vertices at level 
### Part c
>Write pseudocode for a version of the algorithm that runs in time $O(d^{k/2})$ where $k$ is the collaborative distance of $s$ and $t$. Justify the running time of your algorithm

```
def BidirectionalDistance(G,s,t):
	if s == t:
		return 0
	
	for v in V:
		visited_s[v] = FALSE
		visited_t[v] = FALSE
		dist_s[v] = infty
		dist_t[v] = infty
		
	visited_s[s] = TRUE
	visited_t[t] = TRUE
	dist_s[s] = 0
	dist_t[t] = 0
	
	create empty queues Qs, Qt
	Enqueue(Qs, s)
	Enqueue(Qt,t)
	
	while Qs is not empty and Qt is not empty:
		is size(Qs) \leq size(Qt):
			answer = Expand-Frontier(G,Qs,visited_s,dist_s,visited_t,dist_t)
		else:
			answer = Expand-Frontier(G, Qt, visited_t, dist_t, visited_s, dist_s)
	if answer \neq NIL:
		return answer
	return NIL
```

```
def Expand-Frontier(G,Q, visited_me, dist_me, visited_other, dist_other):
	u = Dequeue(Q)
	
	for each v in Adj[u]:
		if visited_me[v] = FALSE:
			visited_me[v] = TRUE
			dist_me[v] = dist_me[u] + 1
			
			if visited_other[v] = TRUE:
				return dist_me[v] + dist_other[v]
				
			Enqueue(Q,v)
	return NIL
```

##### Proof
- let $k$ be the collaborative distance between $s$ and $t$
	- each BFS frontier grows at the branching factor $\leq d$
		- a normal BFS would explore up to $d^k$ vertices in the worst case
			- bidirectional BFS will explore $d^{k/2} vertices from each side in expectation
				- $O(d^{k/2} + O^{k/2}) = O(d^{k/2})$
	