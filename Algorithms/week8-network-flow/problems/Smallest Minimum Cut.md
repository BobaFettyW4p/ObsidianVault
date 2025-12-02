# Prompt
>You are given a directed flow network $G=(V,E,c)$ with source $s$ and sink $t$, and a maximum flow $f$

### Part a
>Write pseudocode for an algorithm that finds a minimum $s-t$ cut that minimizes the number of edges crossing the cut. Your algorithm must run in $O(Ef)$ time.

```
def MinCuMinEdges(G=(V,E,c)):
	for each e in V.E:
		c(e) = c(e) * |E| + 1
	f = Ford-Fulkerson(G)
	G_f = the residual graph of G with respect to f
	dist = BFS(G_f, s)
	sol =[]
	for vertex in G.V:
		if dist[vertex] < \infty:
			add vertex to sol
	return sol
```
### Part b
>Prove the correctness of your algorithm
- our algorithm works by multiplying each weight by $|E|+1$
	- since there can be at most $E$ edges crossing a cut, multiplying any positive value by $|E|+1$ guarantees that any individual edge will have a lower weight than any two added edges
	- thus, any min cut we can find will have the fewest number of edges
- once we have done this, we simply need to find a min cut
	- this is done by running Ford Fulkerson and then running BFS from the beginning node will find a min but which must be the min cut with the fewest edges
### Part c
>Analyze it's running time

- adjusting the weight of every edge in the graph can be done in $O(E)$ time
- Ford-Fulkerson itself is $O(Ef)$ time
- BFS runs in $O(V+E)$ time
- this gives us a running time of $O(E) + O(Ef) + O(V+E) = O(Ef)$