## Why Change the Model?
- A homogenous poisson process has a constant arrival rate:
$$\lambda(t) = \lambda$$
- this is useful, but often too simple
- Real systems have a rate that often depends strongly on the time of day
	- call centers
	- emergency rooms
	- web traffic
	- road traffic
	- electrical demand

## From Constant Rate to Time-Varying Rate

![[Pasted image 20260801120735.png]]

## Definition: Non-Homogenous Poisson Process
- Let $N(t)$ count arrivals up to time $t$
- A non-homogeneous Poisson process has an instantaneous rate $\lambda(t)$
- For a small time interval $\Delta t$,
$$P(\text{one arrival in } [t, t+ \Delta t]) \approx \lambda(t) \Delta t$$
Key idea:
> The process is still locally Poisson, but the local rate changes with time

## Small-Interval Probabilities
- For a very small $\Delta t$,
$$P(N(t + \Delta t) - N(t) = 1) = \lambda(t) \Delta(t) + o(\Delta t)$$
$$P(N(t + \Delta t) - N(t) = 0) = 1 - \lambda(t) \Delta t + o(\Delta t)$$
$$P(N(t + \Delta t) - N(t) \geq 2) = o(\Delta t)$$
- independent increments are retained, but stationarity is not

## The Cumulative Rate
- the time-varying rate accumulates over time:
$$\Lambda (t) = \int_0^t \lambda(u) du$$
- Interpretation:
$$E[N(t)] = \Lambda(t)$$
- the area under the rate curve is the expected number of arrivals
![[Pasted image 20260801121605.png]]

## Distribution of the Count
- For a non-homogenous Poisson process,
$$N(t) \sim Poisson(\Lambda(t))$$
- more generally:
$$N(b) - N(a) \sim Poisson(\int_a^b \lambda(u)) du$$

- To compare with part 1:
	- If $\lambda(t) = \lambda$ is constant, then $\Lambda(t) = \lambda t$, and we recover the homogenous case

## Interarrival Times Are No Longer Identical
- in the homogenous process,
$$T_1, T_2, T_3, \dots \sim^{i.i.d} Exp(\lambda)$$
- in a non-homogeneous process, the waiting-time behavior depends on the current time
![[Pasted image 20260801121935.png]]

## Simulation Strategy
- The goal: generate arrivals from a process whose rate if $\lambda(t)$
- One practical method is *thinning*:
	- find an upper bound $\lambda_{max}$ for the rate
	- generate candidate arrivals from a homogenous process with rate $\lambda_{max}$
	- keep a candidate at each time $t$ with probability:
$$\frac{\lambda(t)}{\lambda_{max}}$$
### Thinning: The Picture

![[Pasted image 20260801123416.png]]

### Thinning Algorithm
- Initialize $t=0$
- Generate $U_1 \sim Uniform(0,1)$
- Advance to the next candidate time:
$$t \leftarrow t - \frac{1}{\lambda_{max}} \log U_1$$
- Generate $U_2 \sim Uniform(0,1)$
- Accept the candidate if:
$$U_2 < \frac{\lambda(t)}{\lambda_{max}}$$
- repeat until the final simulation time is reached

## Why Thinning Works
- Candidate arrivals occur at the high constant rate $\lambda_{max}$
- At time $t$, only the fraction:
$$\frac{\lambda(t)}{\lambda_{max}}$$
are accepted.
- Therefore, the local accepted rate is:
$$\lambda_{max} \times \frac{\lambda(t)}{\lambda_{max}} = \lambda(t)$$
- Key idea:
> Generate too many events, then randomly discard just enough to leave the desired time varying rate

## Example Rate Function

- A simple daily arrival pattern might be:
$$\lambda(t) = 3 + 2 \sin^2 (\frac{\pi t}{12}), 0 \leq t \leq 24$$
![[Pasted image 20260801123829.png]]

## Putting the Four Parts Together
- random event arrivals
- Poisson process: constant rate
- Exponential interarrival times
- Memoryless property
- Non-homogenous Poisson process
- Simulation and live examples

## Key Takeaways
- a non-homogenous Poisson process allows the rate to vary with time
- The cumulative rate $\Lambda(t) = \int_0^t \lambda(u) du$ replaces $\lambda t$
- Counts remain Poisson distributed, but with parameter $\Lambda(t)$
- Thinning is a simple simulation method: propose at rate $\lambda_{max}$, accept with probability $\lambda(t)/\lambda_{max}$
