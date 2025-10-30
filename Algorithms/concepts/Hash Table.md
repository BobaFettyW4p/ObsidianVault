- dictionary problem
	- maintain a dynamic set of items, each with a key in a table
	- should support 3 operations:
		- $Insert(item)$: add an item to the set
		- $Delete(item)$: remove an item from the set
		- $search(key)$: return the item with its key (if it exists)
		- we can assume all items have distinct keys
		- goal is for all operations to run in $O(1)$ time
- How can we compactly store a small dynamic subset of a large set of data items in a table?
	- Ex. changing list of ~250 IP addresses
		- an IP address is 32 bits in 4 8 bit fields
		- can index an array by IP address and achieve $O(1)$ search times
			- downside: lots of wasted space (many table entries will be empty)
		- Linked list of 250 records:
			- inserting and deleting records will be incredibly fast
				- searching for records will not meet our criteria
		- Solution: hash table
			- give a "short name" to each IP address and store them in a table of size 250
- Issues arising from hash tables:
	- How do we assign "short names"?
	- What if two entries receive the same name?

### General set up
- consider a universe of values
	- $u = {1,\dots,2^w}$
		- integers between $1$ and $2^w$ where $w = 32$
	- a hash function $h$ maps: $u -> T[0 \dots m-1]$
	- key $x \in u$ is stored in table $T$ at the index specified by the hash function:
		- $T[h(x)]$
	- ideally, hash functions will map $x$ and $y$ such that $x \neq y$ to $h(x) \neq h(y)$
		- if this is not followed, this is called a collision

#### Dealing with collisions
- two main methods:
	- chaining
	- open addressing

##### Chaining
- Consider a table $T[0..m-1]$
- $T[i]$ contains a list of all keys $i$ in $T$ such that $h(x) = i$
- Search time to locate a key:
	- $O(1+l(x))$where $x$ is the number of keys in $T$ such that $h(x) = h(y)$

- In order to hash $n$ items into a table of size $m$:
	- worst case: all $i$ hash to the same position
		- list $T[i]$ has length $n$
		- $\Theta(n)$
	- expected time:
		- naive:
			- assume data is distributed uniformly
				- for all $x \in u$, for all $i \in {1, \dots, m-1}$, $p_{h \in H}(h(x) = i)$ = $\frac{1}{m}$
#### Universal Hash Function
> For all $x,y \in U$ where $x \neq y, p_{h \in H}(h(x)) = h(y)) \leq \frac{1}{m})$

- multiply add hashing:
	- Choose a prime integer such that $p > | u |$
	- define set $Z_p^* = {1, \dots, p-1}$
	- define set $Z_p = 0,1,\dots,p-1$
	- define a hash function:
		- choose integer $a \in Z_p$ at random
		- choose integer $b \in Z_p^*$ at random
		- $h_{a,b}(x) = (ax + b mod\ p) mod m$
		- Worst case:
			- keys $k_i \neq k$
			- $p_{a,b} = h(k_j) = \frac{1}{m}$
			- let ^^ be event $R_{k_i,k_j}$
			- the expected number of collisions is as follows:
			- $E[num\ collisions\ with\ k_i] = E[\Sigma R_{k_i,k_j}]$
			- $= \Sigma E[R_{k_i,k_j}]$
			- $= \Sigma p(R_{k_i,k_j} = 1)$
			- $= \Sigma \frac{1}{m}$
			- $=\frac{n}{m}$