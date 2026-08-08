- previously, we've assumed a constant arrival rate

## Why Change the Model
- A constant arrival rate:

$$\lambda(t) = \lambda)$$
- this is useful, but often too simple
- Real systems:
	- call centers
	- emergency rooms
	- web traffic
	- road traffic
	- electrical demand
		- the rate often depends strongly on the time of day

## Definition
- Let $N(t)$ count the arrivals up to time $t$
	- a non-homogenous Poisson process has an instantaneous rate $\lambda(t)$
- For a small time interval $\Delta t$,
$$P(\text{one arrival in }[t,t + \Delta t]) \approx \lambda(t) \Delta t$$
>The process is still locally Poisson, but the local rate changes with time

## Small-Interval Probabilities
- for a very small $\Delta t$,
- independent increments are retained, but stationarity is not

## The Cumulative Rate
- the time varying rate accumulates over time:
