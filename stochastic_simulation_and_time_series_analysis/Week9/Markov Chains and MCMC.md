# The Foundation: Expectation as an Average
- Suppose $X$ has probability density $p(x)$. Then:
$$E[f(X)] = \int f(x) p(x) dx \approx \frac{1}{N} \sum_{i=1}^N f(X_i)$$
- provided that

$$X_1, X_2, \dots, X_N \sim^{iid} p$$

> If we can draw independent samples according to $p$, then an integral weighted by $p$ can be estimated simply by evaluating $f$ at those samples and averaging

> A potentially difficult integral becomes a sampling problem

### Example: Rolling Five Dice
- imagine an experiment where we roll five fair dice
- One possible outcome is $x = \{3, 6, 2, 5, 6\}$
- The size of the sample space is $|S| = 6^5 = 7776$
- Define:
$$\begin{equation}
f(x) = 
\begin{cases}
1, & \text{if the sum of the dice is greater than 20,} \\
0, & \text{otherwise.}
\end{cases}
\end{equation}$$
> The expected value fo this function is the probability that the sum exceeds 20

# The Enumeration Approach
- for fair dice, every configuration has the same probability:
$$p(x) = (\frac{1}{6})^5$$
- So the exact probability is:
$$\sum_{\text{all configurations of x}} f(x)p(x)$$

- since $f(x)$ is either 0 or 1, this is just:
$$\sum_{\text{configurations x such that sum(x)>20}} (\frac{1}{6})^5$$
>Enumerate every possible roll, keep the ones where the sum exceeds 20, and add their probabilities

# The Sampling Approach
- instead of enumerating all 7776 configurations, we can roll the five dice many times
	- this produces independent configurations:
$$X_1, X_2, \dots, X_N$$
- for each roll, compute:
$$\begin{equation}
f(X_i) = 
\begin{cases}
1, & \text{if the sum exceeds 20,} \\ 
0, & \text{otherwise}
\end{cases}
\end{equation}$$
- then estimate the probability by:
$$\frac{1}{N} \sum_{i=1}^N f(X_i)$$
> The average of the 0-1 outcomes is just the fraction of rolls where the event happen

#### Now make the probabilities unequal: loaded dice

- suppose each die is loaded:
$$P(1) = P(2) = P(3) = P(4) = P(5) = 0.1, \quad P(6) = 0.5$$
- configurations no longer have equal probability
	- for example:
$$P(6,6,6,6,6) = (0.5)^5$$
- but:
$$P(1,2,3,4,5) = (0.1)^5$$
- So $(6,6,6,6,6)$ is:
$$(\frac{0.5}{0.1})^5 = 3125$$
- times as likely as $(1,2,3,4,5)$

# Enumeration Still Works, but the Weights Matter
- for loaded dice, the exact answer is still:
$$E[f(X)] = \sum_{\text{all configurations x}} f(x)p(x)$$
- but now $p(x)$ depends on the particular configuration
	- for example:
$$p(6,6,6,6,6) = (0.5)^5$$
- whereas:
$$p(1,2,3,4,5) = (0.1)^5$$

> The enumeration approach examines every configuration and explicitly weights each one by its probability

> This is the discrete version of an integral weighted by a probability distribution

# Sampling from loaded dice automatically applies the weights
- if we repeatedly roll the loaded dice, more probable configurations appear more often
	- we do not ened to manually multiply each sampled configuration by $p(x)$ in the final average
		- this weighting occurs through frequency:
$$\text{more probable configurations} \rightarrow \text{more frequent samples}$$
- Therefore:
$$\frac{1}{N} \sum_{i=1}^N f(X_i)$$
- still estimates:
$$\sum_x f(x)p(x)$$
- provided the rolls really are drawn according to $p$
> Sampling according to the probabilities lets us count sampled configurations uniformly

##### Now imagine 100 loaded dice
- a configuration is now:
$$x = (x_1, x_2, \dots, x_{100})$$
- the number of possible configurations is:
$$6^{100} \approx 6.5 \times 10^{77}$$
- enumeration is very cost intensive
	- generation one configuration is still easy:
		- just roll 100 dice
> The state space can be impossible to enumerate while sampling remains simple

# But how many samples do we need?
- for our 0-1 function, let
$$q = E[f(X)] = P(event)$$
- with $N$ independent samples,
$$\hat{q} = \frac{1}{N} \sum_{i=1}^N f(X_i)$$
- and
$$SE(\hat{q}) = \sqrt{\frac{q(1-q)}{N}} \leq \frac{1}{2 \sqrt{N}}$$

| Samples $N$ | Worst-case standard error |
| ----------- | ------------------------- |
| 100         | 0.05                      |
| 10000       | 0.005                     |
| 100000      | 0.0005                    |

# the general Monte Carlo idea
- the dice example has all the pieces:

| Dice language                  | General language                           |
| ------------------------------ | ------------------------------------------ |
| configuration of dice          | state or configuration $x$                 |
| probability of a configuration | probability mass/density $p(x)$            |
| quantity we care about         | function $f(x)$                            |
| weighted enumeration           | $\Sigma_x f(x) p(x)$ or $\int f(x)p(x) dx$ |
| repeated rolls                 | samples $X_i \sim p$                       |
| average of sampled values      | $\frac{1}{N} \Sigma_i f(X_i)$              |
> Monte Carlo replaces explicity weighted enumeration with representative sampling

# The usual Monte Carlo story starts with independence

> The Classical law of large numbers

- If:
$$X_1, X_2, \dots, X_n \sim^{iid} p$$
- then:
$$\frac{1}{n} \sum_{k=1}^n f(X_k) \rightarrow E_p[f(X)]$$
- this was the version studied last week
	- it is natural to internalize the wrong less:
> Maybe independence is essential.

# Markov's insight: dependence need not destory averages
- For certain dependent sequences, including many finite-state Markov chains,
$$\frac{1}{n} \sum_{k=1}^n f(X_k) \rightarrow E_{\pi} [f(X)]$$
- where $\pi$ is the stationary distribution of the chain
	- the samples are not independent
	- the next state is generated from the current state
	- but long-run averages can still settle down

> Dependent samples can be tolerated

![[Pasted image 20260817114636.png]]

# Where we go next
- Markov chains describe dependent dynamics
	- Some chains have stable long-run behavior
		- Monte Carlo turns integrals into averages
			- MCMC designs a chain to sample a hard distribution
> I: the mechanics of Markov chains

> II: why Metropolis needed them

### A short Markov-chain refresher
- Let $X_n$ denote the state of a system after step $n$
- a first order Markov chain satisfies
$$P(X_{n+1} = j | X_n = i, X_{n-1}, \dots, x_0) = P(X_{n+1} = j | X_n = i)$$
- the transition probabilities can be collected in a matrix:
$$P_{ij} = P(X_{n+1} = j | X_n = i)$$
- the next state depends on the current state
- earlier history adds no addiitonal information

> "Memoryless" does not mean independent. Successive states are generally dependent


# The property we need: a stationary distribution
- a probability distribution $\pi$ is *stationary* for a Markov chain if:
$$\pi P = \pi$$
- in other words:
$$X_n \sim \pi \rightarrow X_{n+1} \sim \pi$$
- the chain itself moves from state to state, but its *distribution* no longer changes
> For a suitable chain, $\pi j$ also describes the long-run fraction of time spent in state $j$

# The crucial fact: dependent averages can still converge
- Suppose:
$$X_1, X_2, \dots$$
- are generated by a suitable Markov chain whose stationary distribution is $\pi$
- the samples are generally **not independent**
$$X_1 \rightarrow X_2 \rightarrow X_3 \rightarrow \dots$$
- but the ergodic theorem gives, under suitable conditions,
$$\frac{1}{N} \sum_{i=1}^N f(X_i) \rightarrow E_{\pi}[f(X)]$$
> Independent sampling was sufficient for averaging to work. It was not necessary.