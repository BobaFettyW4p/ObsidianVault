## Big O notation
- establishes a loose upper bound on the runtime of an algorithm
- Ex. $4n^2 + 22n + 12$ is $O(n^2)$ for all $n \geq 1$
- $f(x) = O(g(x))$ if $lim_{x -> \infty}| \frac{f(x)}{g(x)} < \infty$ 
- A constant function is always big O of any other constant as long as it is not negative

## Big $\Omega$ Notation
- establishes a loose lower bound on the runtime of an algorithm
- $T(n) = \Omega(g(n)) if\ (\exists n_0) (\exists c>0)$ such that $0 \leq c*g(n) \leq T(n)$ for all $n \geq n_0$


## Big $\Theta$ Notation
- establishes a lower and upper bound
- $T(n) = \Theta(g(n))$ if $T(n) = O(g(n))$ and $T(n) = \Omega(g(n))$