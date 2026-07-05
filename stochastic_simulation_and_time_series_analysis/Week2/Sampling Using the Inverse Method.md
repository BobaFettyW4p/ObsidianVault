## Sampling from an Arbitrary PDF
- how do you sample a random value from an arbitrary PDF or probability mass function?
- the *inverse transform method* is a simple technique appropriate for both discrete or continuous random variables
	- first, consider the continuous case
		- suppose you want to generate a random variable $X$ with an arbitrary CDF, $F(x)$
		- Assuming you have a way to generate $U(0,1)$, a uniform random number between 0 and 1, then the following algorithm applies:
- in order to generate a PDF, you need to generate the CDF first
	- integrate the PDF to receive the CDF
	- we call the CDF $F(x)$ by convention

- the Algorithm:
	- generate $U$ from $U(0.1)$
	- find a value of $X$ such that $F(X) = U$
- e.g. we want to solve $X = F^{-1}(U)$
- there are a few limitations:
	- if the CDF is given as a continuous function it must be invvertible, which is not always the case
	- if the CDF is discrete we interpret finding the value of $X$ to mean the first index of the CDF such that $U \leq CDF$
	- note that non-invertible functions can be discretized even though this process is often expensive
		- other approaches may be superior

##### Proof
$$P\{X \leq x_0\} = P \{ F^{-1}(U) \leq x_0\}$$
$$= P\{F(F^{-1}) \leq F(x_0)\} \text{ since F(x) is increasing}$$
$$=P\{U \leq F(x_0)\}$$
$$=F(x_0) \text{ since U is uniform}$$

##### Example
- Consider the PDF:
$$p(x) = e^{-x} \text{ where} 0 < x < \infty$$
- the CDF is:
$$F(x) = 1 - e^{-x}$$
- the inverse, which yields a random variable $X$, is:
$$F^{-1}(U) = - \log (1-U) = X$$
- a discrete form of the algorithm is extremely useful for tabulated functions or those that cannot be inverted

- integrating the PDF gives you the CDF, which lets you procede with the algorithm