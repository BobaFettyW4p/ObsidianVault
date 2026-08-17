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


#### Same goal, two ways to generate the samples
##### Independent samples
- If:
$$X_1, X_2, \dots \sim^{iid} p$$
- then the law of large numbers gives
$$\frac{1}{N} \sum_{i=1}^N f(X_i) \rightarrow E_p[f(X)]$$

##### Markov samples
- If $X_1,X_2, \dots$ come from a suitable Markov chain with stationary distribution $p$,
$$\frac{1}{N} \sum_{i=1}^N f(X_i) \rightarrow E_p[f(X)]$$
- both of these methods lead to the same destination
	- but they have a different way of generating the samples

> This is the fact that makes MCMC possible

# Three different reasons we might be averaging samples

|                         | What we have                                                     | Why averaging works                                                    |
| ----------------------- | ---------------------------------------------------------------- | ---------------------------------------------------------------------- |
| IID data                | independent samples from some independent distribution $p$       | Law of large numbers                                                   |
| Observed Markov process | Dependent observations from an ergodic Markov process            | Ergodic theorem                                                        |
| MCMC                    | A distribution $p$ we want to sample, but cannot sample directly | Construct an ergodic Markov chain whose stationary distribution is $p$ |
- An important distinction
> In the first two cases, the stochastic process already exists and we observe it. In MCMC, we deliberately create the stochastic process

# Same destination, different ways of generating the samples

##### Independent samples
- If
$$X_1, X_2, \dots \sim^{iid} p$$
- then the law of large numbers gives
$$\frac{1}{N} \sum_{i=1}^N f(X_i) \rightarrow E[f(X)] = \int f(x)p(x) dx$$
##### Dependent Markov samples
- if $X_1, X_2, \dots$ come from a suitable ergodic Markov process whose stationary distribution is $p$, then:
$$\frac{1}{N} \sum_{i=1}^N f(X_i) \rightarrow E[f(X)] = \int f(x) p(x) dx$$

# MCMC reverses the direction of the problem
- for ordinary data analysis, we often start with observations and ask:

> What can their average tell us about the process that generated them?

- MCMC starts from the opposite end
	- we have a distribution $p$ and want
$$E[f(X)] = \int f(x) p(x) dx$$
- but drawing independent samples from $p$ is difficult
- we deliberately construct a Markov chain with
$$\text{stationary distribution} = p$$
- the ergodic theorem then lets us use its dependent samples just as we would have used independent samples from $p$

> If direct sampling from $p$ is hard, manufacture an ergodic process whose long-run distribution is $p$

# The key inversion
- So far we have asked:

> Given a Markov chain, what is its stationary distribution?

- For MCMC we will reverse the question:

> Given a distribution $\pi$ that we want to sample, can we construct a Markov chain whose stationary distribution is $\pi$?

- if we can, the ergodic theorem lets a single dependent trajectory estimate expectations 

#### Why would we ever want to do that?
- We want to compute $E_p[f(X)] = \int f(x) p(x) dx$
	- Monte Carlo turns this expectation into an average of samples drawn from $p$
		- but drawing independent samples directly from $p$ may be difficult
			- construct a Markov chain whose stationary distribution is $p$

> This is where Markov chains become a numerical method

# A real example: the Boltzmann distribution
- Consider a physical system with configuration $x = (x_1, x_2, \dots, x_d)$ and energy $E(x)$
- At temperature $T$, statistical mechanics tells us that the equilibrium probability of configuration $x$ is:
$$p(x) = \frac{1}{Z} e^{-E(x) / (kT)}$$
- where $Z$ is a normalizing constant
- thus, low-energy configurations are more probable than high-energy configurations

> In this case, $p$ is not something we invented for Monte Carlo. It is the probability distribution of the physical system.

# What do we want to calculate?
- suppose $f(x)$ is some physical quantity associated with configuration $x$
	- For example, $x$ might represent:
		- energy
		- magnetization
		- pressure
- it's equilibrium expected value is:
$$E_p[f(X)] = \int f(x) p(x) dx$$
- or, for a discrete configuration space,
$$E_p[f(X)] = \sum_x f(x)p(x)$$

> This is exactly our $f(x) p(x)$ problem

# Ordinary Monte Carlo tells us exactly what to do

- if we could generate independent configurations:
$$X_1, X_2, \dots, X_N \sim^{iid} p$$
- then
$$E_p[f(X)] \approx \frac{1}{N} \sum_{i=1}^N f(X_i)$$
- this is no different in principle from our dice example
- Sample configurations according to their probabilities, then average $f$
	- So why not just sample $X \sim p$

> If we can sample from $p$, estimating the expectation is straightforward

# But how do we draw $X \sim p$
- for the loaded dice, sampling from $p$ was easy:
	- just roll the dice
- For a system of many interacting particles,
$$p(x) = \frac{1}{Z} e^{-E(x)/(kT)}$$
- may be a distribution over an enormous, high-dimensional configuration space
- We can often evaluate the energy of a particular configuration
$$x \rightarrow E(x)$$
- but that does not give us an obvious way to generate an independent configuration $X \sim p$

# Why is the Boltzmann distribution hard to sample?

- the formula may look simple on the surface:
$$p(x) \propto e^{-E(x)/(kT)}$$

- but $x$ may describe thousands of interacting particles
##### Loaded dice
$$p(x_1, \dots, x_N) = \Pi_{i=1}^N p_i (x_i)$$
- the dice are independent
	- sample each die separately

##### Interacting particules
$$E(x) = \sum_{i<j} V(|x_i - x_j)$$
- each particle's energy depends on the positions of other particles
	- the coordinates are coupled
- Given a particular configuration $x$, we may be able to compute:
$$x \rightarrow E(x) \rightarrrow e^{-E(x)/(kT)}$$
- but not clear how to generate a random configuration $X$ with correct probabilities

# But we can do something much easier
- although drawing an independent $X \sim p$ may be difficult, suppose we have two configurations $x$ and $x'$
	- their relative probabilities are:

$$\frac{p(x')}{p(x)} = \frac{\frac{1}{Z} e^{-E(x')/(kT)}}{\frac{1}{Z} e^{-E(x)/(kT)}}$$
- the unknown normalizing constant cancels:
$$\frac{p(x')}{p(x)} = e^{-[E(x')-E(x)]/(kT)}$$
- we may not know how to draw a completely new independent sample from $p$, but we can compare the relative probabilities of two configurations

> Can we turn this into a sampler for $p$?

# The Metropolis idea
- we cannot easily generate a completely new independent sample
$$X \sim p$$
- but starting from a configuration $x$, we can:
	- propose a nearby configuration $x'$
	- compare $p(x')$ with $p(x)$
	- use that comparison to decide whether to move to $x'$
	- repeat
- This produces a sequence:
$$X_0 \rightarrow X_1 \rightarrow X_2 \rightarrow \dots$$
- the samples are dependent, they form a Markov chain

> Design the transition rule so the chain's stationary distribution is $p$


### Metropolis: make a small local proposal

- suppose the current configuration is $x$
	- choose one particle
	- propose moving it a small random distance
	- call the resulting configuration $x'$
	- Compute:
$$\Delta E = E(x') - E(x)$$
> we never need to invent a completely new configuration from scratch

### The Metropolis acceptance rule
- for:
$$\pi(x) \propto e^{-E(x)/(kT)}$$
- use a symmetric proposal and accept $x'$ with probability:
$$\alpha(x, x') = min(1, e^{-\Delta E/(kT)})$$
##### If $\Delta E \leq 0$$
- lower energy:
$$\alpha = 1$$
- always accept
##### If $\Delta E > 0$$
- higher energy:
$$\alpha = e^{-\Delta E / (kT)}$$
- sometimes accept

#### Why accept moves to worse states?
- if we accepted only lower-energy moves, the process would descend until trapped in a local minimum
- at finite temperature, higher-energy configurations must occur too
	- Metropolis allows for uphill moves, but suppresses them according to:
$$e^{-\Delta E/(kT)}$$
##### Temperature matters
- Small T: uphill moves are rare
- Large T: uphill moves are more common

> MCMC is sampling, not optimization

### A remarkable convenience: the normalization cancels
- Suppose:
$$\pi(x) = \frac{w(z)}{Z}$$
- where $Z$ is extremely difficult to calculate. then:
$$\frac{\pi(x')}{\pi(x)} = \frac{w(x')/Z}{w(x)/Z} = \frac{w(x')}{w(x)}$$
- for the Boltmann distribution:
$$\frac{pi(x')}{\pi(x)} = e^{-[E(x')-E(x)]/(kT)}$$

> MCMC often requires only an unnormalized target distribution

# Why does this acceptance rule product the right distribution?

- a convenient sufficient condition for stationarity is *detailed balance*:
$$\pi(x)P(x, x') = \pi(x', x)$$
- at equilibrium, probability flow from $x$ to $x'$ is balanced by flow from $x'$ to $x$
- summing over all $x$ gives:
$$\sum_x \pi(x) P(x, x') = \pi(x')$$

- which is stationarity
> Detailed balance is not the motivation for Metropolis; it is the mathematical reason the construct works

# Metropolis is engineered to satisfy detailed balance

- let the proposal be symmetric:
$$q(x,x') = q(x',x)$$
- Use:
$$\alpha(x,x') = min(1, \frac{\pi(x')}{\pi(x)}), P(x,x') = q(x,x') \alpha(x,x')$$
- for either ordering of $\pi(x)$ and $\pi(x')$$:
$$\pi(x) P(x,x') = \pi(x')P(x',x)$$

> we deliberately rig the transition rule so that $\pi$ becomes stationary


# Metropolis-Hastings: The generalization

- If:
$$x' \sim q(x'|x)$$
- accept with probability:
$$\alpha(x,x') = min(1, \frac{\pi(x')q(x|x')}{\pi(x)q(x'|x)})$$
- $\pi(x')/ \pi(x)$ favors moves toward more probable states
- the $q$ ratio corrects for proposal asymmetry
- Symmetric $q$ recovers the original Metropolis rule