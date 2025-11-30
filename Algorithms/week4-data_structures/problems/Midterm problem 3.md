## Prompt
> "In this problem, you may assume that a binary search tree node $x$ has fields $x.left$, $x.right$, $x.key$, and $x.p$ (for the parent node, or NIL is $x$ is root). You may assume that the nodes of the BST are distinct"

### Part a
> "Write pseudocode for a recursive algorithm that takes as input the root of a BST $T$ and outputs an array containing the elements of $T$ in sorted order. Your algorithm must run in $O(n)$ time, where $n$ is the number of nodes in $T$."

#### Solution
- *Idea:* we need to traverse the keys of the tree in order. We can do this by recursively traveling down the left, and gradually traversing down the rights in accordance with the BST invariant
```
def BSTSortedKeys(T,A[1..n]):
	BSTSortedKeys(T.left,A[1..n])
	append root[T].key to A
	BSTSortedKeys(T.right,A[1..n])
	return A
```
### Part b
> "Write pseudocode for an algorithm that takes as input a BST $T$ containing distinct integers and returns the closest pair of integers in $T$ (i.e. the pair of nodes whose keys have the smallest absolute different). Your algorithm must run in $O(n)$ time, where $n$ is the number of nodes in $T$."

#### Solution
- *Idea:* we already have a way to get all of the nodes in sorted order in the desired runtime. We can use this to get all of the pairs to test, and then we can traverse them to find the closest pair
```
def ClosestKeyDifferent(T,A[1..n]):
	keys = BSTSortedKeys(T, A[1..n])
	closest_pair = (-\infty,\infty)
	minimum_distance = \infty
	for i=2 to n:
		candidate_difference = A[i] - A[i-1]
		if candidate_different < minimum_difference:
			minimum_difference = candidate_difference
			closest_pair = (A[i-1],A[i])
	return closest_pair
```