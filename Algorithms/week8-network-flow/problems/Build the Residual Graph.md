# Prompt
>You are given the following directed graph $G$ with capacities and current flow |c/f|

```mermaid
graph TD
s -->|5/8| a;
s -->|3/3| b;
a -->|1/2| b;
a -->|4/5| t;
b -->|3/6| t;
```

### Part a
>Construct the residual graph $G_f$. For each edge, state both the forward and backward residual capacities

```mermaid
graph TD
s -->|3/8| a;
a -->|5/8| s;
b -->|3/3| s;
a -->|1/2| b;
b -->|1/2| a;
a -->|1/5| t;
t -->|4/5| a;
b -->|3/6| t;
t -->|3/6| b;
```

### Part b
>List all $s-t$ augmenting paths in the residual graph
- $s -> a - > t$
- $s -> a -> b -> t$
### Part c
>Give the maximum possible residual capacity of the augmenting paths you listed in part b

- for path $s->a->t$, the bottleneck vertex is $(a,t), which has a residual capacity of 1
- for path $s ->a->b->t$, the bottleneck vertex is $(a,b)$, which has a residual capacity of 1