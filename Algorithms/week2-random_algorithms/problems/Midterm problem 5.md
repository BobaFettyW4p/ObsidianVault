## Prompt
>"You are given a sorted circular linked linked list $A[1..n]$ containing $n$ distinct integers, where every element has a "next" pointer to the next larger element, i,e, $A[i] points\ to\ A[i+1] for\ all\ i=1,\dots,n-1$ 
>The largest element's next pointer points to the smallest element (i.e. $A[n]$ points to $A[1]$)
>Given the value of a uniformly random target element in the list, determine the index of the target element in $A$. There are only two ways you can access an element in the list: follow the next pointer from a previously accessed element, or via a given function $\texttt{RAND}$ that returns a pointer to a uniformly random element of the list.
>You may assume that on accessing a node, you learn $node.value$, $node.index, node.next$
>

### Part A
>"Consider the following randomized algorithm that attempts to solve the problem:

```
def RandFind(target,k):
	if k <= 0:
		return 0
	for i in 1..k:
		u = RAND()
		if u.value == target:
			return u.index
```
> "What is the success probability of this algorithm? Justify your answer"

- *Idea:* each call to $\texttt{RAND()}$ hits the target node with probability $1/n$, independent of all others
- Let $X_i$ be the event that the $i$th call finds then target
	- $p(X_i) = 1/n$
		- the probability that a single call fails is $p(fail) = (1- \frac{1}{n})$
		- the probability that $k$ total calls all fail is $p(all\ fail) = (1 - \frac{1}{n})^k$
		- therefore, the success probability is $p(success) = p(all\ fail) = 1 - (1 - \frac{1}{n})^k$

### Part b
>"You are given a circle of unit circumpherence. You pick $k$ points on the circle independently and uniformly at random  and snip the circle at those points, obtaining $k$ difference arcs. Briefly argue that the expected length of any single arc is exactly $\frac{1}{k}$

- *Idea:* the $k$ random points partition the circle into $k$ arcs
- creating $k$ different arcs in this way yield $k$ arcs that all add up to the circumference of the circle
	- as the points that delineate the arcs are selected uniformly at random, we expect each of the created arcs ot cover an equal percentage of the circumphere, i.e. each of the $k$ arcs covers $\frac{1}{k}$ of the total circumphere

### Part c
>"Write pseudocode for a randomized algorithm that finds the target which makes at most $O(\sqrt{n})$ accesses in expectation and always returns the correct answer. Argue that your algorithm has expected $O(\sqrt{n})$ running time

```
def FindIndex(target, A[1..n]):
	k = ceil(sqrt(n))
	samples = []
	for i in 1..k"
		u = RAND()
		samples.append((u.value,u.index, u))
	best_node = None
	best_val_ge = \infty
	smallest_node = None
	smallest_val = \infty
	
	for (val,idx,node) in samples:
		if val < smallest_val:
			smallest_val = val
			smallest_node = node
		if val >= target abd val < best_val_ge:
			best_val_ge = val
			best_node = node
	if best_node is None:
		start = smallest_node
	else:
		start = best_node
		
	cur = start
	while True:
		if cur.value == target:
			return cur.index
		cur = cur.next
```
- this algorithm always returns the correct index
	- the target is somewhere in the circular list, and repeatedly following $next$ will eventually reach it
- expected running time:
	- $k$ calls to $\texttt{RAND()}$ = $\Theta(\sqrt{n})$ accesses
	- scanning the samples list is $\Theta(k) = \Theta(\sqrt{n})$
		- this part partitions the list in $k$ arcs
	- we then define an arc with the greatest value smallest than the target, and the smallest value greater than the target
		- this defines an arc that must contain the target
			- the size of this arc is $\frac{1}{k}$ in expectation
				- we traverse the arc to find this value, which traverses $k$ total nodes at most
					- $O(\sqrt{n})$
	- thus, the expected runtime is $O(\sqrt{n}) + O(\sqrt{n}) + O(\sqrt{n}) = O(\sqrt{n})$
