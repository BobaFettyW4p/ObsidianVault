# Basics

### Random Variables and Notation
- a random variable is:
	- a numerical quantity whose value is determined by the outcome of a random experiment
		- not quite
	- a numerical quantity whose possible values are described by a probability distribution

$$
X = \text{random variable}
$$
$$x = \text{a particular value of X}$$
- Ex.
$$X = \text{height of a randomly selected student}$$
$$x = \text{72 inches}$$
- Key idea:
> $X$ specifies what is being measured, $x$ is one possible value that $X$ may take

### The CDF Accumulates Probability
- The *Cumulative Distribution Function* is:
$$F(x) = P(X \leq x)$$
- For a continuous random variable:
$$F(x) = \int^x_{-\infty} f(t) dt,  f(x) = F'(x)$$
![[screenshots/Pasted image 20260623204726.png]]

- Key idea:
> The Probability Distribution Function describes local density; the Cumulative Distribution Function describes accumulated probability

### Discrete vs. Continuous Random Variables

##### Discrete

![[screenshots/Pasted image 20260623204930.png]]

$$\Sigma_i p(x_i) = 1$$
- probabilities are assigned to individual values

##### Continuous

![[screenshots/Pasted image 20260623205017.png]]

$$\int^{\infty}_{-\infty} f(x) dx = 1$$
- probabilities are assigned to intervals

### For Continuous Variables, Single Points Have Probability Zero
- For a continuously-valued random variable,
$$P(X \in C) = \int_C f(x)dx$$
- for a small interval of width $\epsilon$ centered at $a$,
$$P(a - \frac{\epsilon}{2} \leq X \leq a + \frac{\epsilon}{2}) \approx \epsilon f(a)$$
- As $\epsilon$ approaches 0, the probability goes to zero
![[screenshots/Pasted image 20260623205240.png]]

# Joint PDFs

### Joint Distributions: Probability for Pairs
- For two random variables, a joint density describes the distribution of pairs:
$$f(x,y)$$
- Marginal densities recover the one-variable distributions:
$$f_x(x) = \int^{\infty}_{-\infty} f(x,y)dy,$$
$$f_y(y) = \int^{\infty}_{-\infty} f(x,y)dx$$
![[screenshots/Pasted image 20260623210103.png]]

### Independence Has a Shape
- two variables are independent when the joint density factors:

$$f(x,y) = f_x(x)f_y(y)$$
- if knowing $X$ changes what you expect for $Y$, the variables are dependent
![[screenshots/Pasted image 20260623210302.png]]

- Takeaway:
>Dependence is visible as structure in the joint distribution

### Marginal PDFs and PMFs
- Joint density:
- $$f_{x,y}(x,y)$$ describes the probability density of $X$ and $Y$ together
- Marginal density of $X$:
$$f_x(x) = \int_{-\infty}^{\infty} f_{x,y}(x,y)dy$$
- to obtain the distribution of $X$, we integrate out $Y$
![[screenshots/Pasted image 20260623210715.png]]

- Interpretation
> for a fixed value of $x$, the marginal density $f_x(x)$ is the total probability obtained by summing over all possible values of $y$

### Expected Value: A Weighted Average
- the expected-value is the probability-weighted average of possible outcomes
- Continuous
$$E[X] = \int^{\infty}_{-\infty} xf(x)dx$$
- Discrete
$$E[X] = \Sigma_i x_i p_i$$
![[screenshots/Pasted image 20260623210902.png]]

### Variance: Spread Around the Mean
- Variance measures average squared distance from the mean:
$$Var(X) = E[(X-\mu)^2]$$
- a convenient identity is:
$$Var(X) = E[X^2] - E[X]^2$$
![[screenshots/Pasted image 20260623211033.png]]
- Takeaway:
> Same mean does not imply same uncertainty. Variance captures spread.

### Moments as Distribution Summaries:
- More generally, the $n$th moment is:
$$E[X^n] = \int_{-\infty}^{\infty} x^n f(x)dx$$
- in order, the moments are:
	- *Mean*: location/center of mass
	- *Variance*: spread around the mean
	- *Skewness*: asymmetry
	- *Kurtosis*: tail weak/peakedness
- Takeaway:
> Moments turn a full distribution into a few interpretable summaries


# Example Distributions

### Uniform Distribution
- If $X ~ Uniform(a,b)$, then:

$$f(x) = \frac{1}{b-a}\ if\ a<x<b, 0\ otherwise$$
$$E[X] = \frac{a+b}{2}, Var(X) = \frac{(b-a)^2}{12}$$
- The CDF is:
$$F(x) = \frac{x-a}{b-a}, a<x<b$$
![[screenshots/Pasted image 20260623211900.png]]

### Gaussian Distribution
- If $X ~ N(\mu, \sigma^2), then:
$$f(x) = \frac{1}{\sqrt{2 \pi \sigma}} exp(-\frac{(x - \mu)^2}{2 \sigma^2})$$
- $\mu$ sets the center
- $\sigma$ sets the scale
- $\sigma^2$ is the variance
![[screenshots/Pasted image 20260623212034.png]]

### Weak Law of Large Numbers
- Markov Inequality
$$P{X \geq a} \leq \frac{E[X]}{a}$$
- Proof:
$$E[X]=\int_0^{\infty} xf(x)dx$$
$$=\int_0^a xf(x)dx + \int_a^{\infty} xf(x)dx$$
$$\geq \int_a^{\infty} xf(x)dx$$
$$\geq \int_a^{\infty} af(x)dx$$
$$=\int_a^{\infty} f(x)dx$$
$$=aP\{X \geq a\}$$
###  Markov Inequality
- Imagine a six sided fair die with $E[X] = 3.5$
- Let's use Markov to bound, e.g. $P(x \geq 6)$
- Markov: $P(X \geq a) \leq \frac{E[X]}{a}$
- In this case $P(x \geq 6) \leq \frac{3.5}{6} = \frac{7}{12}$
- Imagine this is not the case and that $P(x \geq 6) > \frac{7}{12}$
$$E[X] = 1P(x=1) + 2P(x=2) + \dots + 6P(x=6) \geq 6P(X=6)$$
- But if $P(X=6) > \frac{7}{12}$ then $E[X] > 3.5$
- Contradiction!

### Chebyshev Inequality
- for any value $k > 0$:
$$P(|X - \mu| \geq k) \leq \frac{\sigma^2}{k^2}$$
- that is, most points are close to the mean
- Proof. Since $(x-\mu)^2$ is a nonnegative random variable, we can apply Markov's inequality with $a = k^2$:
$$P((X-\mu)^2 \geq k^2) \leq \frac{E[(X-\mu)^2]}{k^2}$$
- Since $|X-\mu|^2 \geq k^2$ if and only if $|X - \mu| \geq k$ then the distributions $P(|X-\mu| \geq k)$  and $P((X-\mu)^2 \geq k^2)$ are identical and:
$$P(|X-\mu| \geq k) \leq \frac{E[(X-\mu)^2]}{k^2} = \frac{\sigma^2}{k^2}$$

- the sample average settles down as the number of samples grows:

![[screenshots/Pasted image 20260623213307.png]]

> The average becomes stable, even though individual outcomes remain random

### Why Variance Shrinks

- Let $X_1, X_2, \dots be independent and identically distributed random variables with mean $\mu$ and variance $\sigma^2$
- The sample mean has the same expected value:
$$E[\bar{X}_n] = \mu$$
- but its variance decreases with sample size:
$$Var(\bar{X}_n) = Var(\frac{X_1 + \dots + X_n}{n}) = \frac{\sigma^2}{n}$$
- Chebyshev then gives:
$$P(|\bar{X}_n - \mu| > \epsilon) \leq \frac{\sigma^2}{n * \epsilon^2} \rightarrow 0$$
# Central Limit Theorem

> Averages of indendent and identically distributed random variables become approximately normal

![[screenshots/Pasted image 20260623213848.png]]

- As $n$ grows, the shape becomes Gaussian and the spread shrinks

### The Statement
- For indendent and identically distributed random variables $X_1, X_2, \dots, X_n$ with mean $\mu$ and variance $\sigma^2$:
$$\sqrt{n}(\bar{X}_n - \mu) \rightarrow^d N(0, \sigma^2)$$
- Equivalently, for large $n$:
$$\bar{X}_n \approx N(\mu, \frac{\sigma^2}{n})$$
- the original distribution need not be normal
- the sample mean fluctuates on the scale $\frac{1}{\sqrt{n}}$
- This is why averages, measurement errors, and estimates so often look Gaussian