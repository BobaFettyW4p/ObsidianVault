# Prompt
>"You are given a flow network $G=(V,E,c)$ with integer capacities. You want to find a minimum $s-t$ cut that minimizes the number of edges crossing the cut, not the total capacity.
>To solve this, you modify capacities to encode the lexicographic goal "minimize capacity first, then minimize edges"

### Part a
>"Describe how to define new capacities $c'(e)$ so that computing a standard min-cut on $G'$ returns the minimum-edge minimum cut on $G$

### Part b
>"Why must $c'(e)$ be chosen to be at least one order of magnitude larger than $|E|$?

