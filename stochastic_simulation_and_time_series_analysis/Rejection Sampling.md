- general sampling method
	- alternative method when the Inverse Transform Method isn't suitable
		- ex. if we can't invert the CDF
- you have a PDF that may be unusual in some way, e.g.:
![[screenshots/Pasted image 20260627203222.png]]
- we may have some analytical form of $f(x)$, but we can't invert it
	- we can draw samples from some other distribution
- goal is to sample a random variable $X$ from an arbitrary PDF $f(x)$
- suppose you have a method of sampling from some other PDF, $g(x)$ such that, for some constant $M$:
$$f(x) \leq Mg(x)$$
- then the following algorithm applies:
	- sample $X$ from $g(x)$
	- calculate $\alpha$, the probability of accepting $X$:
$$\alpha = \frac{f(x)}{Mg(x)}$$
	- sample $U$ from $U(0,1)$
	- if $\alpha \leq U$, then accept the value of $X$
		- if not, reject $X$ and repeat