# Probability Density Functions
### A high level overview and numerical sampling strategies

- two main halves
	- *Probability Density Functions (PDFs)*
	- Sampling from an Arbitrary PDF

### Random Variables and Notation
- a random variable is a numerical quantity whose possible values are described by a probability distribution
	- $X$ = a random variable that represents the outcome of a process in a numerical way
	- $x$ = a particular value of $X$
- every random process can be repeated as a trial
	- these trials can be encoded in random variables
- epistemic vs. aleatory uncertainty
	- aleatory is endemic to the problem
	- epistemic uncertainty is related to your lack of knowledge


### Cumulative Distribution Function/Probability Distribution Function
- The cumulative distribution function is $F(x) = P(X \leq x)$
	- for a continuuous random variable, $F(x) = \int_{-\infty}^x f(t) dt, f(x) = F'(x)$
	- the PDF describes local density, the CDF describes absolute density

#### Discrete vs. Continuous random variables
- Discrete
$$
\Sigma_i p(x_i) = 1
$$
Probabilities are assigned to individual values
- Continuous
$$
\int^{\infty}_{-\infty} f(x)dx = 1
$$
Probabilities are assigned to intervals

### PDFs
- for a continuously-value random variable:
$$
P(X \in C) = \int_c f(x)dx
$$
- for a small interval of width $\epsilon$ centered at $a$:
$$
P(a - \frac{\epsilon}{2} \leq X \leq a + \frac{\epsilon}{2}) \approx \epsilon f(a)
$$
- as $\epsilon$ goes to 0, the probability goes to 0
- a continuous random variable at a point has a value of 0
	- there are infinite values between any two values

### Joint PDFs
- for two random variables, a joint density describes the distribution of pairs $f(x,y)$
	- 3d plot signifies the possibility both events occur
- Marginal densities recover the one variable distributions
$$
f_x(x) = \int_{-\infty}^{\infty} f(x,y)dy
$$
$$
f_y(y) = \int_{-\infty}^{\infty} f(x,y)dx
$$

- Two variables are independent when the joint density factors:
$$
f(x,y) = f_x(x)f_y(y)
$$
- joint density
$$
f_{x,y}(x,y)
$$
describes the probability density of $X$ and $Y$ together

- Marginal density of X:
$$
f_x(x) = \int^{\infty}_{-\infty} f_{x,y}(x,y)dy
$$
- to obtain the distribution of $X$, we integrate out $Y$

> For a fixed value of $x$, the marginal density $f_x(x)$ is the total probability obtained by summing over all possible values of $y$

### PDFs: Expected Value
- the expected value is the probability-weighted average of possible outcomes
- Continuous:
$$
E[X] = \int_{-\infty}^{\infty} xf(x)dx
$$
- Discrete:
$$
E[X] = \Sigma_i x_ip_i
$$

### Variance
- Variance measures average squared distance from the mean:
$$
Var(X) = E[(X-\mu)^2]
$$
- A convenient identity:
$$
Var(X) = E[X^2] - E[X]^2
$$
> Same mean does not imply same uncertainty. Variance captures spread.

### Moments as Distribution Summaries
- generally, the $n$th moment is:
$$
E[X^n] = \int_{-\infty}^{\infty} x^n f(x) dx
$$
- Mean: location/center of mass
- Variance: spread around the mean
- Skewness: asymmetry
- Kurtosis: tail weight/peakedness
> Moments turn a full distribution into a few interpretable summaries

### Uniform Distribution

If $X \approx Uniform(a,b)$, then:

$$
f(x) = \frac{1}{b-a} if a<x<b, 0\ otherwise
$$
$$
E[X] = \frac{a+b}{2}, Var
$$

### Gaussian Distribution

### Weak Law of Large Numbers

## Markov Inequality

### Chebyshev Inequality

### Central Limit Theorem

- deals with how sample means are distributed for a certain value of $n$
>Averages of independent and identically distributed random variables becomes approximately normal

- as $n$ grows, the shape becomes Gaussian and the spread shrinks


> Knowing a PDF is not the same as knowing how to generate samples from it

- sampling is the computational bridge from a probability model to simulated data

### Why Do We Want Samplers?
- sampling generations simulations from probability models
	- generate synthetic data from a model
	- estimate statistics: expectations, quantiles, variances
	- propagate uncertainty through a system
	- run a Monte Carlo simulation or stress test
	- perform probabilistic inference, such as Bayesian analysis
$$
p(\Theta|y) = \frac{p(y|\Theta)p(\Theta)}{p(y)}
$$
- sampling lets us use probability models computationally

> There is no universal definition of a good sampler

- it depends on what you want to do with the samples

- an outcome is the result of a single trial of a random experiment
	- single, indivisible observation