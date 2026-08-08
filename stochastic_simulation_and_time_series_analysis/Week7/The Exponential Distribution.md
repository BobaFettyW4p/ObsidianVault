## Review: The Poisson Process
- Poisson process models random event arrivals
- $N(t)$ counts arrivals in $[0,t]$
- Counts are Poisson distributed with parameter $\lambda t$

## From Counts to Waiting Times
- Instead of asking "How many arrivals occur in time $t$?", as "How long until the next arrival?"
- Define $T$ as the waiting time to the next event

## The Exponential Distribution

$$f_T(t) = \lambda e^{-\lambda t}, t \geq 0$$
- Continuous random variable
- A single parameter: $\lambda$
- Models Poisson interarrival times

## Distribution Function
$$F_T(t) = P(T \leq t) = 1 - e^{- \lambda t}$$
$$P(T > t) = e^{-\lambda t}$$

## Mean and Variance

$$E[T] = \frac{1}{\lambda}$$
$$Var(T) = \frac{1}{\lambda^2}$$
- larger $\lambda$ means shorter expected waiting times

## Memoryless Property

$$P(T>s+t | T > s) = P(T > t)$$
- the future waiting time is independent of how long you have already waited

## Connection to the Poisson Process
- Poisson process => exponential interarrival times
- independent exponential waiting times generate a Poisson process

## Simulation
- Generate successive waiting times...

$$T_i \sim Exp(\lambda)$$
- ...and accumulate them to obtain arrival times

## Summary
- exponential distribution models waiting times
- mean waiting time is $1/\lambda$
- the exponential distribution is memoryless
- forms the foundation of discrete-event simulation

