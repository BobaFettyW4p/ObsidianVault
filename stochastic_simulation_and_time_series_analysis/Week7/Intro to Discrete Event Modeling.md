- transition to part 3 of the course
- up to this point, two copmlementary perspectives
	- probability and random variables
		- gave us the tools to describe uncertainty
	- time series models, AR, MA, ARMA, forecasting
		- describe how observed data evolves over time
- third perspective: instead of modeling a sequence of observations, model the underlying events that generate these systems
- Poisson process, fundamental model in many fields
	- very canonical
	- unifying foundation for the next couple lectures

## Discrete Event Modeling
- basic idea:
	- a system changes state only at *event times*
		- between events, nothing relevant changes
		- events often occur at unpredictable times
		- we model both *what happens* and *when it happens*
- examples:
	- customers arrive at a store
	- packets enter a network
	- machines fail
	- calls reach a help desk
	- particles decay
- these models tend to be very computationally efficient, and mimic real events very well

## Random Arrivals
- for an arrival process, two natural questions:
	- how many events occur in a fixed time interval?
	- how long until the next event occurs?
- we will focus on event counts now
	- next lecture will focus on waiting times

## The Poisson Process
- the *Poisson process* is the standard model for events that arrive randomly at a constant average rate
- the rate $r$ is the average number of events per unit time
- if the interval length is $t$, the expected number of events is:
$$ \lambda = rt$$
- larger $r$ means arrivals are more frequent
- larger $t$ means more time for arrivals to occur
- the count model depends on the product $rt$

## Counts in a Fixed Interval
- Let:
$$N(t) = \text{the number of events in the interval [0,t]}$$
- For a Poisson process with rate $r$:
$$N(t) \approx Poisson(rt)$$
- The Poisson probability mass function:
$$P\{N(t) = k\} = e^{-rt} \frac{(rt)^k}{k!}, k = 1,2,3, dots$$
- not important to memorize, just memorize what it represents
	- the mean and the variance are both $rt$

## Connection to the Binomial Model
- one way to build intuition is to divide time into many small subintervals:
- Binomial count:
$$P\{K = k\} = \binom{n}{k} p^k (1-p)^{n-k}$$
- $n$ possible arrival opportunities
- each succeeds with probability $p$
- expected count is $np$

- Poisson limit:
	- Keep $np = \gamma$ fixed while making $p$ small and $n$ large:
$$\binom{n}{k} p^k (1-p)^{n-k} \rightarrow e^{-\lambda} \frac{\lambda^k}{k!}$$
## Why Smaller Time Steps Help
- If the expected number of arrivals in one hour is $\lambda = 4$:
	- $p=0.5$ gives $n=8$ trials
	- $p=0.25$ gives $n=16$ trials
	- smaller $p$ gives finer time resolution
- in the limit, the artificial grid disappears
	- the time becomes continuous, what we identified as the Poisson process arrives naturally

## Interpreting the Formula
> Suppose customers arrive at an average rate of $r=4$ per hour. What is the probability of exactly $k$ arrivals in one hour?

$$P\{N(1) = k\} = e^{-4} \frac{4^k}{k!}$$

$$P\{N(1) = 0\} = e^{-4}$$
$$P\{N(1) = 1\} = 4e^{-4}$$
$$P\{N(1) = 4\} = e^{-4} \frac{4^4}{4!}$$
$$P\{N(1) \geq 1\} = 1 - e^{-4}$$
### What the model assumes:
-  the Poisson process is not just a formula. It encodes modeling assumptions
	- constant rate: the average arrival rate does not change over time
	- independent increments: arrivals in disjoint intervals are independent
	- No simultaneous arrivals: over a tiny interval, more than one event is negligible
	- Counts depend only on length: a one-hour interval behaves like any other one-hour itnerval
- these assumptions are often an approximation, but they are a powerful starting point

## Simulating a Poisson Process
- a simulation produces one possible realization of the arrival process
- Grid based approximation:
	- break time into small steps
	- at each step, generate a random number
	- record an arrival with small probability $p$
	- count or plot the resulting events

## From Counts to Waiting Times
- the Poisson process tells us how many events occur in a fixed interval
- the next natural question is different:
	- How long do we wait until the next event?
- That waiting time has an exponential distribution

# Key Takeaways
- A Poisson process models random event arrivals at constant rate $r$
- The number of events in time $t$ is Poisson with parameter $rt$
- The Poisson distribution can be understood as a limiting case of a binomial model
- Sample paths are random timelines of event arrivals
- the next lecture studies the time between consecutive events