# Markov's Law of Large Numbers for Dependent Variables
- Classical LLN (Bernoulli)
	- the sample average of independent trails converges to the expected value
- Common belief (early 1900s)
	- Independence was essential for the LLN to hold
- Markov (1906)
	- proved the LLN for certain *dependent* sequences
		- finite-state Markov chains
- Why? To show that statistical regularities can arise even without independence

# What Markov Actually Proved
- The Law of Large Numbers holds for a restricted class of dependent sequences:
	- finite state
	- homogenous
	- irreducible and aperiodic
	- First-order Markov chains
- If these conditions hold:
$$\frac{1}{n} \sum_{k=1}^n f(X_k) \rightarrow^{a.s} E_{\pi}[f]$$
- where $\pi$ is the stationary distribution
- Not a result for all dependent sequences
	- strong, long-range dependence can break the LLN

# A Philosophical Debate
- Pavel Nekrasov: the LLN is tied to free will without independence, it would fail
	- appealed to religious thinkers who saw the LLN as a mathematical reflection of moral freedom
- Markov's rebuttal: dependence does not invalidate the LLN
	- In effect: Statistical regulatiries are compatible with deterministic rules

# Markov's 1913 Demonstration
- the goal: show the LLN working in a dependent, real-world process
- Data sources: *Eugene Onegin* by Alexander Pushkin
- Method: Treat the text as a sequence of letters; classify each as a vowel or consonant

# Turning Pushkin into Data
- remove punctuation and spaces
- Encode:
	- Vowel = 1
	- Consonant = 0
- Count transitions:
	- Vowel $\rightarrow$ Vowel
	- Vowel $\rightarrow$ Consonant
	- Consonant $\rightarrow$ Vowel
	- Consonant $\rightarrow$ Consonant
- This yields a $2 \times 2$ transition matrix
	- a first order Markov chain

### The Measured Transition Counts

| Transition                        | Count | Probability |
| --------------------------------- | ----- | ----------- |
| Vowel $\rightarrow$ Vowel         | 1107  | 0.109       |
| Vowel $\rightarrow$ Consonant     | 3821  | 0.891       |
| Consonant $\rightarrow$ Vowel     | 3949  | 0.104       |
| Consonant $\rightarrow$ Consonant | 27038 | 0.896       |
- Clear dependence: probability of next letter being a vowel depends on current letter

## Stationary Distribution
- Transition Matrix $P$:
$$P = \begin{matrix} 0.109 & 0.891 \\ 0.104 & 0.896 \end{matrix}$$
- stationary distribution $\pi$ solves $\pi P = \pi$
- Solution: $\pi \approx (0.105, 0.895)$
- Interpretation: long-run proportion of vowels $\approx$ 10.5%

# LLN in Action
- even though letters are dependent:
$$\frac{\text{\# vowels in first n letter}}{n} \rightarrow 0.105 \text{ as } n \rightarrow \infty$$

- this is exactly the *law of large numbers* for a Markov chain
- Shows: Independence is not required for convergence

# The Determinism Angle
- Nekrasov's view: the LLN proves freedom
	- if outcomes are dependent, the law fails
- Markov's counterexample:
	- dependent, rule-bound process can still obey the Law of Large Numbers
- Philosophical takeaway:
	- the Law of Large Numbers is not a signature of metaphysical freedom
	- Order and regularity can emerge from purely deterministic or mechanistic rules

# Why This Still Matters?
- Markov's 1913 analysis was one of the first *data-driven* stochastic models
- It connects:
	- probability theory
	- statistical physics
	- philosophy of science
- The same insight underlies:
	- weather models
	- speech recognition
	- DNA sequence analysis

# Markov Chains, MCMC, and Numerical Integration
- Markov chains: future depends only on present
- Monte Carlo: estimate quantities via randomness
- MCMC: use a Markov chain to sample hard distributions
- Key use: estimating high-dim integrals $E_p[f(X)]$

![[Pasted image 20260807201451.png]]

# What is a Markov Chain?
- States $X_1, X_2, \dots$ with $P(X_{n+1}| X_n)$ fixed
- Transition matrix $P$
	- rows sum to $1$
- Often converges to a stationary distribution $\pi$
![[Pasted image 20260807201554.png]]

# Why Markov Chains Matter in Computation
- structure to explore huge state spaces
- $\pi$ captures long-run behavior; sample from $\pi$ to estimate expectations
- useful when enumeration or direct sampling is impossible

![[Pasted image 20260807201651.png]]

# Numerical Integration: The Goal
- estimate $I = \int f(x) dx$ or $\int f(x) p(x) dx$
- determinstic rules struggle in high dimensions
- MC: draw $X_i \sim p$ and average $f(X_i)$; error $\sim 1/\sqrt{N}$

![[Pasted image 20260807201750.png]]

# Monte Carlo Integration (i.i.d)
- Draw $X_1, \dots, X_n \sim^{i.i.d} p$
- Estimate $\mu = E_p[f(X)]$ by $\hat{mu} = \frac{1}{N} \sum_{i=1}^N f(X_i)$
	- error $\sim N^{-1/2}$
- If $X_i$ is uniform on $\Omega$ with density $1/|\Omega|$:
$$I = \int_{\Omega} f(x) dx = \hat{I} = |\Omega| \hat{\mu}$$
- Notation:
	- $X_i$ is a random variable
	- $X^{i}$ is a realized sample
	- MCMC: draws are dependent but marginally $\sim p$
		- use $N_{eff}$

![[Pasted image 20260807202313.png]]

# Importance Sampling
- focus samples where $f(x)$ contributes most
- Estimator:
$$I \approx \frac{1}{N} \sum \frac{f(X_i)}{p(X_i)}$$
- lower variance for same $N$ if $p \propto |f|$

![[Pasted image 20260807203557.png]]

# Enter MCMC
- Build a chain with stationary distribution $p(x)$
- After burn in, draws resemble $x \sim p$
	- correlated
- Works even when $p$ is known up to a constant

![[Pasted image 20260807203643.png]]

# Metropolis-Hastings (High-Level)
- Propose $x' \sim q(x \rightarrow x')$
- Accept with:
$$\alpha = min(1, \frac{p(x') q(x' \rightarrow x)}{p(x) q(x \rightarrow x')})$$
- else stay at $x$
- Repeat
	- chain has $p$ as the stationary distribution
![[Pasted image 20260807204254.png]]

# Why MCMC Works (Ergodicity)
- If irreducible and aperiodic:
$$\frac{1}{N} \sum_{i=1}^N f(X_i) \rightarrow E_p[f(x)]$$
- correlated samples: manage mixing/effective sample size
- Diagnostics: running means, ACF, multiple chains

![[Pasted image 20260807204441.png]]

# Big Picture Connection
- Markob chains: mechanism to explore $p$
- Monte Carlo: estimator for integrals/expectations
- MCMC: practical bridge for complex, high-dim $p$
- Choose $q$, check mixing, monitor error $\sim 1/\sqrt{N_{eff}}$

![[Pasted image 20260807204540.png]]

# Introduction
- Consider a sequence of random variables:
$$X_1, X_2, \dots, X_n$$
- Interpret $X_n$ as "the state of a system at time $n$"
- $X_n \in \{1 \dots N \}$, i.e. denote the state as an integer even though it can represent anything
- Define $P_{ij}, i, j = 1, \dots, N$ as the probability that the system in state $i$ will transition to state $j$
	- The $P_{ij}$ are called *transition probabilities*
- For a Markov Chain, $P_{ij}$ is independent of the past state of the system
	- e.g. is the same for all times

## Example
- Let's start with a very simple example
- Imagine two positions, call them 1 and 2
- A person starts at one position and each hour transitions to a new position (or stays in current one) with probabilities given by:
$$P_{11} = 0.9, \text{ probability of moving from 1 to 1}$$
$$P_{12} = 0.1, \text{ probability of moving from 1 to 2}$$
$$P_{21} = 0.2, \text{ probability of moving from 2 to 1}$$
$$P_{22} = 0.8, \text{probability of moving from 2 to 2}$$
- and a particular sequence might look like this:
$$ 1, 1, 1, 2, 2, 2, 1, 1, 1, 1, 1, 1, 2$$
- Note that we can write the transition probabilities as a matrix:
$$P = \begin{matrix} .9 & .1 \\ .2 & .8 \end{matrix}$$
- where the rows sum to one, indicating that the system has to be in some state at each time step
- Some key questions about this process:
	- given an initial state, what are the probabilties of being in each state after $2,3, \dots , k$ steps?
	- as $k \rightarrow \infty$, is a steady state reached where the fractions of time spent in each state are constant?
	- If so, how do we calculate those values?
- Turns out that a unique steady state (*stationary probabilities*) are guaranteed if the Markov chain is *irreducible* and *aperiodic*

# Graph Representation
- Graph representation uses edge weights to encode transition probabilities
- self-loops are possible
	- perhaps even typical
- outgoing edges must sum to 1 from each node

![[Pasted image 20260808101437.png]]

##### Example of three graph state (self-loops implied)

![[Pasted image 20260808101459.png]]

## Simple Markov Chain code

```
def markov_chain(X, pdf, nsteps):
	N = pdf.shape[0]
	cdf = np.cumsum(pdf, axis=1)
	pi = np.zeros((N, 1))
	
	for i in range(nsteps):
		rn = np.random.rand()
		tmp = cdf(X-1, L)
		
		j = 1
		cutoff = tmp[j-1]
		while rn > cutoff:
			j += 1
			cutoff = tmp[j-1]
			
		x = j
		pi[j-1] += 1
	return pi
```

# Multi-Step Probabilities
- let's create a $3 \times 3$ example to calculate multi-step probabilities
- Below is a transition probability table that expresses the probability of one of three weather conditions on a sequence of days: rainy, snowy or nice

$$P = \begin{matrix} .5 & .25 & .25 \\ .5 & 0 & .5 \\ .25 & .25 & .5 \end{matrix}$$

- We can then easily calculate the probability of snow in 2 days given it is raining today:
$$p_{13}^2 = p_{11}p_{13} + p_{12}p_{23} + p_{13}p_{33}$$
- this can be read as a sum of conditional probabilities:
	- the probability of transition from 1 to 1 times the probability of transitioning from 1 to 3 PLUS
		- the probability of transitioning from 1 to 2 times the probability of transitioning from 2 to 3 PLUS
			- the probability of transitioning from 1 to 3 times the probability of transitioning from 3 to 3
- this generalizes to:
$$p_{ij}^2 = \sum_{l=1}^r p_{il}p_{lj} = P^2$$
- more generally for any value of $k$:
$$p_{ij}^k = P^k$$

# Stationary Probabilities for Weather Example
- Squaring $P$ yields the two-step probabilities between states $i$ and $j$:
$$P^2 = \begin{matrix} .4375 & .1875 & .375 \\ .375 & .25 & .375 \\ .375 & .1875 & .4375 \end{matrix}$$
- raising our matrix $P$ to a large power (20 determined by trial and error) yields an approximation of the steady state probabilities:

$$P^{20} = \begin{matrix} .4 & .2 & .4 \\ .2 & .2 & .4 \\ .4 & .2 & .4 \end{matrix}$$
- If we consider an initial state of $x = [ 1\text{ } 0\text{ } 0 ]$ (or equivalently $[ 0 \text{ } 1 \text{ } 0]$ or $[ 1 \text{ } 0 \text{ } 0]$)
- we get the stationary probabilities as:
$$\pi = xP^k = [ -.4 \text{ } .2 \text{ } .4]$$

# Properties and Definitions
- Some event must occur, so $P_{ij}$ must sum to one:
$$\sum_{j=1}^N P_{ij} = 1, i = 1, \dots, N$$
- Denote $\pi j$ as the long-run proportion of time that the system spends in state $j$. The $\pi j$ satisfies:
$$\pi j = \sum_{i=1}^N \pi_i P_{ij}, j = 1, \dots, N$$
$$\sum_{j=1}^N \pi j = 1$$
- a Markov Chain is *irreducible* if every state can eventually be reached by every other state
- A Markov Chain is said to be *aperiodic* if for some $n > 0$ and some state $j$:
$$P(X_n = j | X_0 = j) > 0 \text{ and } P(X_{n+1} = j | x_0 = j) > 0$$
- that is, any state should in principle be possible at any time
	- as opposed to a bipartite graph

## Stationary Probabilities
- in the typical case whre we can assume an aperiodic, irreducible Markov process, it can be shown that:
$$\pi j = \lim_{n \rightarrow \infty} P(X_n = j), j = 1, \dots, N$$
- e.g., the $\pi j$ gives the long term probabilities of the system being in a given state
- Another way to think of this is that after many iterations of the state, the $\pi j$ represents the fractions of time the systems was in the $j$'th state, assuming that any state can be visited at any time (no bipartitie graphs)

## Markov Matrices
- it's useful to take a linear algebra perspective
	- A Markov matrix has the following properties:
		- all entries $\geq 0$
		- columns sum to 1
		- Example:
$$A = \begin{matrix} .1 & .01 & .3 \\ .2 & .99 & .3 \\ .7 & 0 & .4 \end{matrix}$$
- key properties:
	- $\lambda = 1$ is an eigenvalue
	- All other $|\lambda_i| < 1$

# Eigenvalue 1
- How does linear algebra show us this?
- Notice that for a Markov matrix the columns of $A - I$ add to 0
- Example:
$$A - I = \begin{matrix} -.9 & .01 & .3 \\ .2 & -.01 & .3 \\ .7 & 0 & -.6 \end{matrix}$$
- Also use that the eigenvalues of $A^T$ are equal to the eigenvalues of $A$
	- then it is clear that the vector $[1 \text{ } 1 \text{ } 1]^T$ is an eigenvector with eigenvalue $1$
		- it's then easy to show that other eigenvalues must have absolute value less than $1$

### Simple Example
- imagine we know the probabilities each year that a person will move from Massachusetts to California, and vice versa
	- enter these probabilities into our Markov matrix:
$$P = \begin{matrix} .9 & .2 \\ .1 & .8 \end{matrix}$$
- the matrix $P$ states the following:
	- in a given year, the probability of a person staying in California is $.9$, and the probability of moving to Massachusetts was $.1$
		- similarly, the probability of a person staying in Massachusetts is $.8$, and of moving to California was $.2$

> Question: What are the steady state probabilities?

- the steady state is the population split where the flows in and out of each state exactly balance out
	- the population moving from CA to MA match the population moving from MA to CA exactly
- to solve: we need $\pi$ such that $P \pi = \pi$ and $\pi_1 + \pi_2 = 1$
$$.9 \pi_1 + .2 \pi_2 = \pi_1$$
$$.2 \pi_2 = .1 \pi_1$$
$$\pi_1 = 2 \pi_2$$
- given that $\pi_1 + \pi_2 = 1$:
$$2 \pi_2 + \pi_2 = 1$$
$$\pi_2 = \frac{1}{3}, \pi_1 = \frac{2}{3}$$

# Drawing Sample from a given Probability distribution Function (PDF)
- Suppose we want to generate the value of a random variable $X$ distributed according to $P(X=j) = p_j, j = 1, \dots, N$
- Can we generate an aperiodic Markov chain with limiting probabilities $p_j, j = 1, \dots, N$ and run chain for $n$ steps to obtain the value of $X_n$?
- We want to estimate typically:

$$E[h(x)] = \sum_{j=1}^N h(j)p_j$$
- This can be done using the estimator:
$$\frac{1}{n} \sum_{i = 1}^n h(X_i)$$
# Irreducibility
- reiterating the key conditions for our Markov Chain more formally:
	- a Markov Chain is said to be *irreducible* is, $\forall a, b \in \chi$
		- where $\chi$ denotes the state space:
$$P(x_t = b | x_o = a) > 0$$
- conceptually, a chain is irreducible if it is possible to reach any state regardless of the starting point
	- this does not say that you can reach the state from any other state
- irreducibility is a necessary condition for many of the key Markov theorems

# Aperiodicity
- another equivalent way to think of aperiodicity that is easy to check is as follows: 
	- an irreducible Markov chain is *aperiodic* if the following condition holds for any starting point $a$:
$$gcd(t: P(x_t = a | x_o = a) > 0) = 1$$
> "The greatest common divisor of all times for which a return is possible to node $a$ must be one"

- Conceptually, this indicates that is possible for any node to be visited at any time
	- e.g. certain nodes are not restricted to odd times

# Ergodic Theorem for Markov Chains
- the intuition on Markov chains is that they *fill the event space* is run for enough iterations
- This is formalized with the following:
	- If $\{x_0, x_1, \dots, x_n\}$ is an irreducible discrete Markov chain wtih stationary distribution $\pi$, then as $n \rightarrow \infty$:
$$\frac{1}{n} \sum_{i = 1}^n f(x_i) \rightarrow E[f(x)]$$
- note that the $x_i$ are not independent
	- typically correlated
	- can be both a benefit and a drawback depending on the application

# Long Time Probability
- If the Markov Chain is *aperiodic* for all $x, x_0$ in the event space, then as $n \rightarrow \infty$:

$$P(x_n = X | x_0 = X_0) \rightarrow \pi(x)$$
- note that wihtout aperiodicity the Markov Chain may still have stationary probabiltiies
- However, aperiodicity is required to ensure that the next draw from the chain represents a draw from the underlying PDF
	- subtle difference, but important
	- this is logical as periodicity essentially forces an ordering at each step

# Calculating Graph Period
- for an irreducible, strongly connected graph $G$, we can calculate its period as follows:
	- perform a depth-first search of $G$
	- for each $e$ in $G$ that connects a vertex on level $i$ of the depth-first search tree to a vertex on level $j$, let $k_e = j - i - 1$
	- Compute the greatest common divisor of the set of numbers $k_e$

# Detailed Balance - A Special Case of Stationarity
- a probability mass function $\pi$ on $\chi$ satisfies *detailed balance* if, for all $a$ and $b$:
$$\pi_a P_{ab} = \pi_b P_{ba}$$
- Note: if $\pi$ satisfies detailed balance then $\pi$ is a stationary distribution:

$$\pi b = \sum_a \pi_a P_{ab} = \sum_a \pi_b P_{ba} = \pi_b \sum_a P_{ba} = \pi_b$$
- satisfying detailed balance is a very special case of a stationary distribution
	- will not be the case in most cases

# Detailed Balance and the Metropolis Algorithm
- Recall: a stationary distribution $\pi$ satisfies $\pi = \pi P$
- Detailed balance is a stronger condition that guarantees stationarity:

$$\pi_a P_{ab} = \pi_b P_{ba}, \forall a,b$$
- the Metropolis algorithm is designed so its transition probabilities satisfy detailed balance with respect to a chosen target $\pi$
	- therefore, the target distribution $\pi$ is the stationary distribution of the Markov Chain we construct

# Metropolis Algorithm - rigging the Markov Chain to mimic samples from the specified PDF
- we can think of the stationary probabilities $\pi$ as a probability mass function spread across the states
- Given a probability mass function $\pi$ on $\chi$ and $f: \chi \rightarrow R$
	- the goal is to sample from $\pi$ or approximate $E[f(x)]$
		- this is of great interest when $\pi$ or $f$ are extremely complicated
- Metropolis: Construct a Markov chain with stationary probabilities $\pi$, then invoke the ergodic theorem
- Metropolis Hastings: Slightly more general, trial matrix does not need to be symmetric

# Metropolis Algorithm
- begin with a *proposal matrix* $Q, Q_{ab} \in \chi$
	- can be any stochastic matrix but choice affects performance
		- must be symmetric for Metropolis, not Metropolis Hastings
- choose Q
- choose arbitrary $x_O$ in $\chi$
- for $i = 0, 1, \dots, n-1$:
	- Sample $x$ from $Q(x_i, x)$
	- sample $u = U(0,1)$
	- if $u < \frac{\pi (x)}{\pi (x_i)}$, then $x_{i+1} = x$, else $x_{i+1} = x_i$

# Metropolis-Hastings Algorithm
- Choose stochastic matrix $Q$
	- begin with *proposal matrix* $Q, Q_{ab} \in \chi$
		- can be any stochastic matrix, but your choice affects performance
- Choose arbitary $x_0$ in $\chi$
- for $i = 0, 1, \dots, n-1$:
	- sample $x$ from $Q(x_i, x)$
	- sample $u = U(0,1)$
	- if $u < \frac{\pi(x) q(x,x_i)}{\pi (x_i) q(x_i, x)}$, then $x_{i+1} = x$, else $x_{i+1} = x_i$

# Relationship to Stochastic Integration
- a common goal is to numerically estimate an integral:
$$i = \int_a^b f(x)dx$$
- note that this can be written:
$$I = \int_a^b \frac{f(x)}{p(x)} p(x) dx$$
- so long as $p(x)$ is nonzero on $[a,b]$
	- if we define $h(x) = \frac{f(x)}{p(x)}$, then the above integral becomes:
$$I = \int_a^b h(x)p(x)dx$$
- if $p(x)$ is chosen as a PDF, we can interpret the above expression as the expected value of a function $h(x)$ with pdf $p(x)$
	- we know from the weak law of large numbers this can be approximated as:
$$I \approx \sum_{i=1{^N}} h(X_i)$$
- for a large $N$ and with $X_i$ being drawn from $p(x)$

# Choice of P

- this implies we can estimate $I$ by selecting values randomly distributed according to $p(x)$ and use those values to evaluate $h(x)$
- Let's consider a simple choice of $p(x)$: a simple distribution on $[a,b]$
- In this case, $h(x) = f(x)$ and the algorithm starts with $I=0$ and iterates:
	- select $X = U \in [a,b]$
	- evaluate $v = f(X)$
	- $I = I+v$
- this process converges in expectation as $\frac{1}{\sqrt{N}}$, where $N$ is the number of samples
- unlike deterministic methods, the convergence rate is independent of the the dimensionality of the integrand, so Monte Carlo integration becomes potentially the best choice for high-dimensional integrals
- even for higher level integrals, the simple choice of uniform $p(x)$ can make Monte Carlo integration very inefficient for highly localized functions
- For functions where most contributions to the integral come from a small fraction of the domain, this naive Monte Carlo approach chooses many samples whose contribution is close to zero
- A better choice of $p(x)$ yields a technique called *importance sampling*, which could help get around this problem
- Importance sampling coupling with Metropolis-Hastings to select from a better choice of $p(x)$ can dramatically improve accuracy for a given number of samples