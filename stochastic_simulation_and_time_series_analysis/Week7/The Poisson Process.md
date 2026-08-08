## Discrete Event Modeling
- Basic idea:
> A system changes state only at *event times*

- between events nothing, relevant changes
- events often occur at unpredictable times
- we model both what happens, and when it happens
- Examples:
	- when customers arrive to a store
	- packets enter a network
	- machines fail
	- calls reach a help desk
	- particles decay

## Random Arrivals
- for an arrival process, two natural questions are:
	- How many events occur during a fixed time interval?
	- How long until the next event occurs?

![[Pasted image 20260731195923.png]]

## The Poisson Process
- a *Poisson process* is the standard model for events that arrive randomly at a constant average rate
- the rate $r$ is the average number of events per unit time
- if the interval length is $t$, the expected number of events is:
$$\lambda = rt$$
- larger $r$ means arrivals are more frequent
- larger $t$ means more time for arrivals to occur
- the count model depends on the product $rt$

## Counts in a Fixed Interval
- Let:
$$N(t) = \text{ number of events in the interval } [0,t]$$
- For a Poisson process with rate $r$:
$$N(t) \approx Poisson(rt)$$
- Poisson probability mass function:
$$P \{N(t) = k\} = e^{-rt} \frac{(rt)^k}{k!}, k=0,1,2,\dots$$
- the mean and variance of this process are both $rt$

## Connection to the Binomial Model
- one way to build intuition is to divide time into many small subintervale
- Binomial count:
$$P(K = k) = \binom{n}{k} p^k (1-p)^{n-k}$$
- Poisson limit:
$$\binom{n}{k} p^k (1-p)^{n-k} \rightarrow e^{-\lambda} \frac{\lambda^k}{k!}$$

- there are $n$ possible arrival opportunities
- each succeeds with probability $p$
- the expected count is $np$

## Why Smaller Time Steps Help
- if the expected number of arrivals in one hour is $\lambda = 4$:
	- $p = 0.5$ gives $n=8$ trials
	- $p=0.25$ gives $n=16$ trials
	- this holds, so a smaller $p$ gives a finer time resolution
- in the limit, the artificial grid disappears

![[Pasted image 20260731211349.png]]

## Interpreting the Formula
> Example: Suppose customers arrive at average rate $r=4$ per hour. What is the probability of exactly $k$ arrivals in one hour?

$$P(N(1) = k) = e^{-4}\frac{4^k}{k!}$$
$$P(n(1) = 0) = e^{-4}$$
$$P(n(1) = 1) = 4e^{-4}$$
$$P(n(2) = 4) = e^{-4} \frac{4^4}{4!}$$
$$(N(1) \geq 1) = 1 - e^{-4}$$

## What the Model Assumes
- The Poisson process is not just a formula. It encodes modeling assumptions:
	- **Constant rate**: the average arrival rate does not change over time
	- **Independent increments**: arrivals in disjoint intervals are independent
	- **No simultaneous arrivals**: over a tiny interval, more than one event is negligible
	- **Counts depends only on length**: a one-hour interval behaves like any other one-hour interval
- these assumptions are often an approximation, but they are a powerful starting point

## Simulating a Poisson Process
- a simulation produces one possible realization of the arrival proceess
- Grid-based approximation
	- break time into small steps
	- at each step, generate a random number
	- record an arrival with small probability $p$
	- count or plot the resulting events

![[Pasted image 20260731212414.png]]

## From Counts to Waiting Times
- the Poisson process tells us how many events occur in a fixed interval
- The next question is different:
> How long do we wait until the next event?
- That waiting time has an exponential distribution:
![[Pasted image 20260731212732.png]]

## Key Takeaways
- A Poisson process models random event arrivals at a constant rate $r$
- The number of events in time $t$ is Poisson with parameter $rt$
- The Poisson distribution can be understood as a limiting case of a binomail model
- Sample paths are random timelines of event arrivals
- 