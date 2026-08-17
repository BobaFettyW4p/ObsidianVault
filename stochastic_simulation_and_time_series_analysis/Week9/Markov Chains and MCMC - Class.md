- Dependence, Long-Run Averages, and Why Markov MAtters

# The Foundation: Expectation as an Average
- Suppose $X$ has probability density $p(x)$. Then:
$$E[f(X)] = \int f(x)p(x) dx \approx \frac{1}{N} \sum_{i=1}^N f(X_i)$$
- provided that:
$$X_1, X_2, \dots, X_n \sim^{i.i.d} p$$
> If we can draw independent samples according to $p$, then an integral weighted by 4p$ can be estimated simply by evaluating $f$ at those samples and averaging

> a potentially difficult integral becomes a sampling problem

- leverages WLLN to get there
	- incredibly important theorem because this is trivial in comparison to evaluating the integral

### Example: Rolling 5 dive
- imagine an experiment where we roll five fair dice
	- one possible outcome is:
$$x = (3, 6, 2, 5,6 )$$
- The size fo the sample space is $|S| = 6^5 = 7776$
- Define:
$$f(x) = 1, \text{ if the sume of the dice is greater than 20}, 0 \text{ otherwise}$$
- the expected value of this $0-1$ function is the probability that the sum exceeds 20

## The Enumeration Approach
- for fair dice, every configuration has the same probability:

- pi is the stationary probabilities of the Markov chain
