# Prompt
> "Suppose that we have an even-length array of booleans $A[1..n]$ where exactly half of the entries are True and half of the entries are False. Out goal is to find an index $i$ between 1 and $n$ such that $A[i]$ is true. Here is a randomized algorithm for this problem
> Note: $\texttt{RANDOM(1,n)}$ returns an integer in the interval $[1,n]$ chosen uniformly at random. You may assume the running time of $\texttt{RANDOM(1,n)}$ is $O(1)$ "

```
def Algorithm1(A[1..n]):
	j = RANDOM(1,n)
	if A[j] is True:
		return j
	else:
		Algorithm1(A)
```

### Part a
>"Is Algorithm1 Las Vegas or Monte Carlo?

- it is Las Vegas
	- it will always return a correct value, but will require a varying runtime to do it

### Part b
>"What is the expected running time?"

- Let $X_t$ be a random variable such that $X_t = 1$ if $A[i] = True$, and 0 otherwise.
- As our algorithm is Las Vegas, it can be considered a geometric sequence
	- Therefore, $E[X_i] = \frac{1}{p} = \frac{1}{0.5} = 2$
	- $O(2) = O(1)$
		- so the algorithm runs in constant time

### Part c
> " What is the success probability of $\texttt{Algorithm1}$"

- the success probability of a single call is as follows:
	- $p(success) = \frac{\frac{n}{2}}{n} = \frac{1}{2}$
		- it follows that the failure probability of a single call is as follows:
			- $p(failure) = 1 - p(success) = 1 - \frac{1}{2} = \frac{1}{2}$
	- mathematically, the odds of eventual success are:
		- $p + (1-p)p + (1-p)p + \dots$
		- $\frac{1}{2} + \frac{1}{2}\frac{1}{2} + \dots$
		- this is an infinite geometric series:
			- $\Sigma_{k=1}^\infty (\frac{1}{2})^{k+1} = 1$
- this is logical as the idea behind a *Las Vegas* algorithm is that it will return a correct value, but will vary in the total number of calls to return that value
