### From PDFs to Samples

> Knowing a PDF is not the same as knowing how to generate samples from it
> Sampling is the computational bridge from a probability model to simulated data

### Why Do We Want Samplers?
- sampling generates simulations from probability models
	- generate synthetic data from a model
	- estimate statistics: expectations, quantiles, variances
	- propagate uncertainty through a system
	- run Monte Carlo simulations or stress tests
	- perform probabilistic inference, such as Bayesian analysis

$$p(\Theta, y) = \frac{p(y|\Theta)p(\Theta)}{p(y)}$$
- posterior equals likelihood times prior divided by evidence
>Sampling lets us use probability models computationally

>There is no universal definition of a good sampler

- It depends on what you want to do with the samples

### Different Goals, Different Needs

- Estimate $E[f(X)]$ by Monte Carlo - the sampler needs to approximate the target distribution with independent and identically distributed random variables or sufficiently mixing samples
- Visualize a distribution - match the shape or frequencies; true randomness may be optional
- Stress test rare events - Visit extreme cases often enough to reveal failure modes
- Bayesian inference - explore the posterior with correct long-run marginals
- Variance reduction, e.g. QMC - Cover the space evenly with low discrepancy

### Important Sampling and Rare Events
- suppose we need to estimate the impact of a rare but costly event:
	- 100 year flood
	- extreme market move
	- rare engineering failure
- naive sampling may never visit the important region
![[Pasted image 20260624195031.png]]

### Importance Sampling: Correcting the Bias
- Sample from a proposal distribution $q(x)$, but estimate expectations under $p(x)$
$$E_p[f(X)] = \int(x)p(x)dx = \int f(x) \frac{p(x)}{q(x)}q(x)dx$$
$$E_p[f(X)] \approx \frac{1}{N} \Sigma_{i=1}^N f(x_i) \frac{p(x_i)}{q(x_i)}, x_i ~ q$$
- choose $q(x)$ to sample important regions more often
- reweight by $\frac{p(x_i)}{q(x_i)}$ to correct the bias
- the art is choosing $q$ so the estimator has lower variance

##### Purpose-Aware Definition
- a sampler is good if it produces samples that are adequate for the intended task
- Different problems demand different properties:
	- independence?
	- correct marginal distribution?
	- low variance?
	- space-filling coverage?
	- fast mixing?

# Inverse Transform Method

- the inverse transform method turns a uniform random number into a sample from another distribution
- Algorithm:
	- Draw $U ~ Uniform(0,1)$
	- Find $X$ such that $F(X) = U$
	- Return $X = F^{-1}(U)$
![[Pasted image 20260624200023.png]]

### Why It Works?

- If $X = F^{-1}(U)$ and $U ~ Uniform(0,1)$, then $X$ has the CDF $F$
$$P(X \leq x_0) = P(F^{-1}(U) \leq x_o)$$
$$=P(U \leq F(x_0))$$
$$=F(x_0)$$
- uniform randomness plus a CDF gives us the desired distribution

### Example: Exponential Distribution
- Suppose:
$$p(x) = e^{-x}, x>0$$
- Then:
$$F(x) = 1 - e^{-x}$$
- So:
$$X = F^{-1}(U) = -log(1-U)$$
![[Pasted image 20260624200510.png]]

### Discrete Inverse Transform Sampling
- Sample from a discrete distribution:
$$P(X=x) = 0.1\ if\ x=1, 0.2\ if\ x=2, 0.4\ if\ x=3, 0.3\ if\ x=4$$
- Draw $U ~ Uniform(0,1)$
	- Return the smallest $x$ such that $F(x) \geq U$

![[Pasted image 20260624200639.png]]

##### Lookup Procedure
- Draw $U ~ Uniform(0,1)$
- Scan the CDF
- Find the first jump where $F(x_i) \geq U$
- Return $x_i$

![[Pasted image 20260624200805.png]]

# Specialized Techniques
- for specific distributions, techniques may exist to sample more efficiently than general approaches can
- a good example is sampling from a normal distribution using the *Box-Muller* algorithm
- Box-Muller generates pairs of independent, standard, normally distributed (zero expectation, unit variance) random numbers, given a source of uniformly distributed random numbers

### Box Muller: Algorithm
- Suppose $U_1$ and $U_2$ are independent samples chosen from the uniform distribution on the unit interval $(0,1)$
- Let:
$$\Theta = 2 \pi U_1; R = \sqrt{-2 \log U_2}$$
- Then the variables:
$$X = R \cos(\Theta); Y= R \sin(\Theta)$$
are two random selections from the normal distribution with mean zero and variance 1

##### Box Muller: Derivation
- Box Muller is based on a standard trick for integrating a Gaussian. The integral:
$$I = \int_{-\infty}^{\infty} e^{\frac{-x^2}{2}} dx$$
cannot be computed directly by finding the anti-derivative of the integrand, but we can compute:

$$I^2 = \int_{-\infty}^{\infty} e^{\frac{-x^2}{2}}e^{-\frac{y^2}{2}} dxdy$$
$$= \int_{-\infty}^{\infty} e^{-\frac{x^2 + y^2}{2}} dxdy$$
$$= 2\pi$$ using polar coordinates

- this is why Box-Muller produces two samples from a normal distribution
- Start with the PDF of a joint normal distribution. Then $\Theta$ is uniformly distributed:
$$\Theta = 2\pi U_1$$
- and $R$ is distributed as:
$$G(R) = P(R \leq r) = \int^R_{r'=0} \int^{2\pi}_{\Theta=0} \frac{1}{2\pi} e^{-\frac{r'^2}{2}} rdrd \Theta$$
$$= \int^R_{r'=0} e^{-\frac{r'^2}{2}}rdr$$

# Rejection Sampling
- sample under an easy envelope
	- keep proposed points that fall under the target density $f(x)$
	- reject points above $f(x)$ and try again
$$f(x) \leq Mg(x)$$

![[Pasted image 20260624202134.png]]

##### Algorithm
- Goal: sample a random variable $X$ from a target PDF $f(x)$
- Choose an easy-to-sample proposal density $g(x)$ and a constant $M$ such that
$$f(x) \leq Mg(x)$$ for all $x$
- Repeat:
	- Sample X ~ g(x)
	- Sample $U ~ Uniform(0,1)$
	- Accept $X$ if:
$$U \leq \frac{f(X)}{Mg(X)}$$
- otherwise, reject it and try again

##### Efficiency
- rejection sampling is inefficient if $Mg(x)$ is much larger than $f(x)$ over much of the domain
- if $g(x)$ is similar in shape to $f(x)$, then the acceptance probability is higher
- The average acceptance rate is roughly $\frac{1}{M}$when $g$ is normalized and $M$ is tighet
- in practice, rejection sampling is useful because it is simple, general, and easy to code
>Good proposal distribution = fewer rejected samples

