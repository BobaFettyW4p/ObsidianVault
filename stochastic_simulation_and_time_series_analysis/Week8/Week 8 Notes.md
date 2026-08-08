- unscheduled final

- Poisson
- memoryless
	- one question, but pretty obvious
- non-homogeneity of Poisson

- moving forward, simulation part of the class
	- discrete event simulation
	- markov chain, monte carlo
- start linking recorded lectures to discrete event modeling
# Building a Discrete Event Simulator

## From Stochastic Processes to Simulation
- Backgroun from the videos
	- Poisson process
		- random arrivals
	- exponential RVs
		- interarrival times
	- Memorylessness
		- efficient simulation
	- Thinning
		- time-varying arrivals
- 2 assumptions: stationarity and memoryless
	- then arrivals occur in a Poisson distribution

- Counting view
	- number of arrivals in an interval
- versus the Simulation view
	- the times between arrivals
- these are two equivalent descriptions of the same stochastic process


## Ex: a single-server queue
- arrivals -> queue -> server -> departure
- assumptions:
	- Poisson arrivals with rate $\lambda$
	- exponential service times with rate $\mu$
	- one server
	- first come, first served
	- infinite waiting room
- What events can change the state of this system?
	- Arrivals
	- Departures

- time is naturally discretized
- suppose the current simulation close is $t=4.38$
- with Arrival at 6.72 and Departure at 9.16
- What happens between 4.38 and 6.72?
	- nothing, so advancing time by tiny increments wastes computeates

- Discrete event idea
	- the clock jumps directly to the next event
$$t \leftarrow min(t_A, t_D)$$
## Simulation state
- at any time, the simulator only needs to remember a small state
	- $t$ - simulation clock
	- $N(t)$ - the customers currently in the system
	- $t_A$ - the time of the next arrival
	- $t_D$ - the time of the next departure
	- $A_i$ - arrival time of customer $i$
	- $D_i$ - departure time of customer $i$

> The simulator does not need to represent every instant of time. It only needs the current state and the next scheduled events

### Initialization
- initially, the system is empty:
$$t = 0, N(0 ) = 0$$
- Schedule the first arrival:
$$t_A = Exp(\lambda)$$
- there is no departure yet, so set:
$$t_D = \infty$$

## The Event Calendar
- at every step we maintain a small event calendar
	- next arrival is $t_A$, next departure is $t_D$
- find the earliest scheduled event
- advance the clock to that event time
- process the event
- update the event calendar

> A discrete-event simulator is an event-calendar algorithm plus state-update rules

### Arrival Event
- an arrival occurs when:
$$t = t_A$$
- update the state
	- record the arrival time:
$$A_i = t$$
	- Increment the number in the 

