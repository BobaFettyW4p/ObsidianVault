- introduced Poisson process for random arrivals
	- how many events occur during a fixed period of time?
	- study the same process form a more useful perspective
		- focus on the time between arrivals
			- two viewpoints are equivalent, but lead to different models

## From Counts to Waiting Times
- instead of asking "How many arrivals occur in time $t$?", ask "how long until the next arrival?"
	- define $T$ as the waiting time to the next event

## The Exponential Distribution

$$f_T(t) = \lambda e^{-\lambda t}, t \geq 0$$
- continuous random variable
- single parameter: $\lambda$
- models Poisson interarrival times
- not a new process, just a different way to describe the Poisson process

## Distribution Function

$$F_T(t) = P(T \leq t) = 1 - e^{-\lambda t}$$
$$P(T > t) = e^{- \lambda t}$$
## Mean and Variance
- $E[T] = \frac{1}{\lambda}$
- $Var(T) = \frac{1}{\lambda^2}$
	- larger $\lambda$ means shorter expected waiting times
- faster arrival processes yield shorter, less variable wait times

## Memoryless Property

$$P(T > s + t\ |\ T > s ) = P(T > t)$$
- suppose we've already waited $s$ units of time and the event hasn't occurred
	- intuitively, this carries some information on the total wait
		- mathematically, it doesn't


### Connection to the Poisson Process
- Poisson process <====> exponential interarrival times
- independent exponential waiting times generate a Poisson process
- 

## Simulation
- Generate successive waiting times:
$$T_i \approx Exp(\lambda)$$
- accumate them to obtain arrival times

# Summary
- Exponential distribution models waiting times
- Mean waiting time is $\frac{1}{\lambda}$
- the exponential distribution is memoryless
	- elapsed wait time has no impact on future wait time
- forms the foundation of discrete-event simulation
	- can model wait times without modeling the in-between time
