Let $f(x) = x, g(x) = x^2$, then $f(x) = O(g(x))$
- Proof
	- $lim_{x -> \infty} \frac{x}{x^2} = lim_{x -> \infty}\frac{1}{x} = 0$

- Theorem: $4^x \neq O(2^x)$
	- $lim_{x -> \infty} \frac{4^x}{2^x}$ 
	- $lim_{x -> \infty} \frac{(2^2)^x}{2^x}$
	- $lim_{x -> \infty} 2^x = \infty$
	- and the theorem is proven as the limit is not bounded

- Theorem: $f(x) = O(g(x)) \iff g(x) = \Omega(f(x))$
  - Let $f,g$ be functions such that $f(x) = \Theta(g(x))$ if there are constants $c_1 >0 \land c_2 >0$ and a non-negative integer $x_0$ such that $c_2*|g(x)| \leq | f(x) | \leq c_1* |g(x)|$ for all $x \geq x_0$
  - 