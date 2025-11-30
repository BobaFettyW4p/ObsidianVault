---
title: Asymptotic Notation 1
type: proof
course: algorithms
week: 1
topic: divide and conquer
tags:
  - algorithms
  - divide-and-conquer
  - week1
  - proof
related:
  - [[week1-divide_and_conquer/concepts/Asymptotic Notation|Asymptotic Notation]]
  - [[week1-divide_and_conquer/concepts/Space and Time Complexity|Space and Time Complexity]]
  - [[week1-divide_and_conquer/Week1 Dashboard|Week1 Dashboard]]
---

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

- *Definition:* $f(x) = \Theta(g(x))$ if $lim_{x -> \infty} | \frac{f(x)}{g(x)} | < \infty$ and $lim_{x -> \infty} | \frac{f(x)}{g(x)} > 0$

- *Example:* $10x^3 - 20x + 1 = \Theta(x^3)$
	- *Proof:* 
		- $lim_{x->\infty} \frac{10x^3-20x+1}{x^3}$
		- $lim_{x->\infty} 10 - \frac{20}{x^2} + \frac{1}{x^3} = 10$
		- since the limit is a positive constant, the two functions are $\Theta$ of each other
- *Example:* Is $\frac{x}{ln\ x} = \Theta(x)$?
	- $lim_{x->\infty} \frac{\frac{x}{ln\ x}}{x} = lim_{x -> \infty} \frac{1}{ln\ x} = 0$
	- $\therefore,$ it is not $\Theta$
