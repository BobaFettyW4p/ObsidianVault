# Prompt
>"You are given a directed weighted graph $G = (V,E,w)$ with nonnegative weights. You may use one coupon that allows you to traverse one edge for free. You want the minimum cost paht from $s$ to $t$.


### Part a
>"Describe how to construct a new graph $G'$ such that running Dijkstra's algorithm on $G'$ directly gives the correct answer
- for each vertex, we will create two vertices, $v_{used}$ and $v_{unused}$, to indicate paths where the coupon has been used, and not yet used. For every edge $(u,v,w)$ in $G$, we will create the following edges:
	- $(u_{unused},v_{unused},w)$, which indicates the coupon has not yet been used and will not be used on this edge
	- $(u_{unused},v_{used},w)$, which indicates the coupon being used on this edge
	- $(u_{used},v_{used},w)$ which indicates the coupon has already been used
	- note: $(u_{used},v_{unused},w)$ is not created, as once the coupon has been used, it is not possible to get another one by the constraints of the problem
- once this graph has been created, we will run Dijkstra's from $s_{unused}$ to $t_{used}$  
### Part b
>"Write pseudocode for an algorithm that creates this graph and finds the shortest path

```
def OneCoupon(G=(V,E,w),s,t):
	create new graph G'
	for vertex v in G.V:
		create vertex v_used in G'
		create vertex v_unused in G'
	for edge e=(u,v) in G.E:
		create edge (u_unused,v_unused,w(u,v)) in G'
		create edge (u_unused,v_used,0) in G'
		create edge (u_used,v_used,w(u,v)) in G'
	dist = Dijkstra's(G',s_unused,t_used)
	return min(dist[t_used], dist[t_unused]) // note: in a bounded problem where all weights are positive, it will be sufficient to return the distance to t_used - it will be guaranteed to be at worst equal to t_unused
```
### Part c
>"State how many vertices and edges $G'$ has in terms of $|V|$ and $|E|$
- for each vertex in $G$, $G'$ will have 2 vertices: one representing paths where the coupon has already been spent, and one where it has
- for each ($u,v$) in $G$, 3 additional edges will be created, corresponding to an edge where the coupon was unused before and is not used on the edge, one where the coupon was unused before and is used on this edge, and one representing the state where the coupon was used prior to this edge
- Therefore, $G'$ has $2|V|$ vertices, and $3|E|$ total edges

### Part d
>"Why does the shortest path in $G'$ correspond to a valid solution in the original graph?

- in $G'$, every vertex is split into two "copies"
	- one where the coupon has not been used, and one where it has
	- similarly every edge is split into "three" copies
		- one where the coupon has not been used yet, and is not used on that edge
		- one where the coupon has not yet been used, and is used on this edge
		- one where the coupon has already been used, and is not used
- for any simple $p$ in $G$, there are two equivalent paths: one to $t_{unused}$, which will have an identical weight to $p$, and one to $t_{used}$, which will use the coupon and therefore have a different weight, but will traverse similar weights
- this modified graph is able to utilize Dijkstra's algorithm to find the shortest path, which it will to resolve the problem