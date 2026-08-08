
# From Stochastic Processes to Simulation
- background from the videos
	- Poisson process
		- random arrivals
	- Exponential RVs
		- interarrival times
	- Memorylessness
		- efficient simulation
	- Thinning
		- time-varying arrivals

> How do we combine these stochastic building blocks to simulate an entire dynamic system?

Interarrival times $\rightarrow$ arrival events $\rightarrow$ State updates $\rightarrow$ Performance measures

# Two Views of a Poisson Arrival Process

##### The Counting View
$$N(t + \Delta t) - N(t)$$
- Number of arrivals in an interval:
$$\sim Poisson(\lambda \Delta t)$$
##### The Simulation View
$$X_1, X_2, X_3, \dots$$
- Times between arrivals
$$\sim Exp(\lambda)$$

> These are two equivalent descriptions of the same stochastic process


# Example System: A Single-Server Queue
- arrivals $\rightarrow$ queue $\rightarrow$ server $\rightarrow$ departures
- Model assumptions:
	- Poisson arrivals with rate $\lambda$
	- Exponential service times with rate $\mu$
	- One server
	- First-come, first-served
	- Infinite waiting room
> What events can change the state of this system?

- only two event types:
	- arrival
	- departure

# Why Discrete Event Simulation?

- Suppose the current simulation clock is $t=4.38$:

| Event     | Scheduled Time |
| --------- | -------------- |
| Arrival   | 6.72           |
| Departure | 9.15           |
> What happens between $t=4.38$ and $t=6.72$?

- Nothing. So advancing time by tiny increments wastes computations

> The Discrete-event idea:
> $t \leftarrow min(t_A,t_D)$
> The clock jumps directly to the next event

# Simulation State
- at any time, the simulation only needs to remember a small state:

| Variable | Meaning                           |
| -------- | --------------------------------- |
| t        | simulation clock                  |
| N(t)     | customers currently in the system |
| $t_A$    | scheduled time of next arrival    |
| $t_D$    | scheduled time of next departure  |
| $A_i$    | arrival time of customer $i$      |
| $D_i$    | departure time of customer $i$    |
> The simulator does not need to represent every instant of time. It only needs the current state and the next scheduled events

# Initialization
- initially, the system is empty:
$$t=0, N(0) = 0$$
- Schedule the first arrival:
$$t_A = Exp(\lambda)$$
- there is no departure yet, so set:
$$t_D = \infty$$
- thus,
$$min(t_A, \infty) = t_A$$

# The Event Calendar
- at every step we maintain a small event calendar:

| Event          | Scheduled Time |
| -------------- | -------------- |
| Next arrival   | $t_A$          |
| Next departure | $t_D$          |
- the process:
	- find the earliest scheduled event
	- advance the clock to that event time
	- process the event
	- update the event calendar

> A discrete-event simulator is an event-calendar algorithm plus state-update rules

# Arrival Event
- an arrival occurs when:
$$t = t_A$$
- state update:
	- record the arrival time $A_i = t$
	- Increment the number in the system:
$$ N(t) \leftarrow N(t) + 1$$
	- schedule the next arrival:
$$t_A \leftarrow t + Exp(\lambda)$$

- server logic:
	- if the server was idle, begin service immediately
	- then schedule a departure:
$$t_D \leftarrow t + Exp(\mu)$$
	- otherwise, the customer waits in the queue

# Departure Event
- a departure occurs when:
$$t = t_D$$
- state update:
	- record the departure time $D_i = t$
	- Decrement the number in the system:
$$N(t) \leftarrow N(t) - 1$$
- next departure:
	- if $N(t) = 0$, no one is in service:
$$t_D \leftarrow \infty$$
- if $N(t) > 0$, immediately start service for the next customer:
$$t_D \leftarrow t + Exp(\mu)$$

# Main Simulation Loop

```
initialize state
initialize event calendar
while simulation is not finished:
	t = min(t_A, t_D)
	if t_A <= t_D:
		process arrival
	else:
		process departure
```

# A Short Event Trace
- Print the first few events for debugging and intuition:

| Step | Event     | Time | $N(t)$ after event | Next event(s)              |
| ---- | --------- | ---- | ------------------ | -------------------------- |
| 1    | arrival   | 0.62 | 1                  | $t_A = 1.41, t_D = 1.12$   |
| 2    | departure | 1.12 | 0                  | $t_A = 1.41, t_D = \infty$ |
| 3    | arrival   | 1.41 | 1                  | $t_A = 1.78, t_D = 2.09$   |
| 4    | arrival   | 1.78 | 2                  | $t_A = 2.51, t_D = 2.09$   |
| 5    | departure | 2.09 | 1                  | $t_A = 2.51, t_D = 2.44$   |

# What Should We Measure?
- Simulation is not justa bout generating events, it is about estimating system performance
- Customer-level quantities
	- waiting time in queue
	- total time in system
	- fraction of customers who wait
	- maximum waiting time
- System-level quantities
	- maximum queue length
	- average number in system
	- average number waiting
	- server utilization
> For realistic systems, these quantities are often much easier to estimate by simulation than to compute analytically

# Using the Simulator As An Experiment
- define traffic intensity:

$$\rho = \frac{\lambda}{\mu}$$
| Case             | Interpretation                  | Expected Behavior |
| ---------------- | ------------------------------- | ----------------- |
| $\rho < 1$       | service capacity exceeds demand | stable queue      |
| $\rho \approx 1$ | system near capacity            | long delays       |
| $\rho > 1$       | demand exceeds capacity         | queue grows       |

# Validating the Simulator
- for the simple queue we have just simulated, exact analytical results are available
- M/M/1 queue
	- first M: Markovian arrivals (equivalently: Poisson arrivals or exponential interarrival times)
	- second M: Markovian service (exponential service times)
	- 1: One server
- Model parameters:
$$ \lambda = \text{arrival rate}, $\mu = \text{service rate}, \rho = \frac{\lambda}{\mu}$$
- where $\rho$ is the *traffic intensity* (server utilization)

- for an M/M/1 queue with
$$\lambda < \mu$$
- the following steady-state quantities are known exactly:
$$W = \frac{1}{\mu - \lambda}$$
$$L = \frac{\rho}{1 - \rho}$$
$$W_q = \frac{\rho}{\mu - \lambda}$$
$$L_q = \frac{\rho^2}{1 - \rho}$$
- $W = \text{average time in the system}$
- $W_q = \text{average waiting time in the queue}$
- $L = \text{average number in the system}$
- $L_q = \text{average number waiting in the queue}$

# Looking Ahead
- todays model only had two event types

| System            | Possible event types                     |
| ----------------- | ---------------------------------------- |
| Manufacturing     | machine failure, repair, job completion  |
| Inventory         | demand, replenishment, stockout          |
| Healthcare        | arrival, admission, discharge, transfer  |
| Transportation    | vehicle arrival, signal change, boarding |
| Computing Systems | job arrival, service completion, timeout |
> The engine is the same. Only the state variables and event types become more sophisticated

### Summary
Probability model $\rightarrow$ event types $\rightarrow$ state variables $\rightarrow$ event calendar $\rightarrow$ simulation loop $\rightarrow$ performance measurements


# A More Realistic Example
- suppose we would like to evaluate a traffic intersection
	- cars arrive randomly from each direction
	- signals determine who may proceed
> Goal: evaluate signal timing policies

## What Changes the System?
- Question: What events can occur?
- Random
	- NS arrival
	- EW arrival
- Scheduled
	- Green begins
	- Yellow begins
	- Red begins
- Events don't have to be random

## Simulation State
- Simulation clock
- NS queue length
- EW queue length
- Current signal state
- Next NS arrival
- Next EW arrival
- Next signal change

> The simulation engine is unchanged; only the state has grown

## Event Calendar

| Event         | Time     |
| ------------- | -------- |
| NS arrival    | $t_{NS}$ |
| EW arrival    | $t_{EW}$ |
| Signal change | $T_L$    |
Earliest Event $\rightarrow$ Advance Clock $\rightarrow$ Process $\rightarrow$ Update Calendar

## Vehicle Arrival Event
- Vehicle arrives
- Is the light green?
- Is a queue already present?
- Proceed immediately or join the queue
- Schedule the next arrival

## Signal Change Event
Green $\rightarrow$ Yellow $\rightarrow$ Red $\rightarrow$ Green
- Update signal state
- Release waiting vehicles
- Update queues
- Schedule next signal change

### Traffic Intersection Simulator: Overall Algorithm'
- Initialization
	- set simulation clock: $t=0$
	- Initialize empty NS and EW queues
	- Choose initial signal phase
	- Schedule first events:
		- NS arrival
		- EW arrival
		- signal change
	- No departures scheduled
$$t_{D,NS} = t_{D,EW} = \infty$$
- Main event loop
	- repeat until arrivals have stopped and both queues are empty
$$t \leftarrow min(t_{A,NS}, t_{A,EW}, t_{D,NS}, t_{D,EW}, t_L)$$
	- select the earliest event
	- advance the simulation clock
	- process that event
	- update statistics
> Only one event is processed during each iteration


### Traffic Intersection Simulator: Event Handlers
- Vehicle Arrival
	- join direction's queue
	- record arrival time
	- schedule next arrival
	- if now first in a green queue, schedule departure
- Vehicle Departure
	- remove first vehicle
	- record delay (current time - arrival time)
	- if another vehicle can clear this green, schedule departure
	- otherwise, set departure time to $\infty$
- Signal Change
	- advance to next phase
	- stop departures from old green direction
	- if new green queue is nonempty, schedule first departure
	- schedule next signal change
> Each handler updates the state and schedules future events

### Example: Stepping Through the Event Calendar
- assume:
	- NS light is initially green
	- both queues are empty
	- vehicle headway = 2 seconds
- Initial event calendar

| Event         | Time (s) |
| ------------- | -------- |
| NS arrival    | 4        |
| EW arrival    | 2        |
| NS departure  | $\infty$ |
| EW departure  | $\infty$ |
| Signal change | 10       |
> Earliest event: EW arrival at $t=2$

### Process the EW Arrival
- Vehicle arrivs while the EW light is red
	- Vehicle joins EW queue
	- Schedule next EW arrival
	- No departure is scheduled
- Updated event calendar

| Event         | Time (s) |
| ------------- | -------- |
| NS arrival    | 4        |
| EW arrival    | 7        |
| NS departure  | $\infty$ |
| EW departure  | $\infty$ |
| Signal change | 10       |
> Earliest event: MS arrival at $t=4$

### Process the NS Arrival
- Vehicle arrives while the NS light is green
	- Vehicle joins NS queue
	- Schedule next NS arrival
	- Schedule an NS departure
- Updated event calendar:

| Event         | Time (s) |
| ------------- | -------- |
| NS arrival    | 9        |
| EW arrival    | 7        |
| NS departure  | 6        |
| EW departure  | $\infty$ |
| Signal change | 10       |

> Earliest event: NS departure at $t=6$

### Process the NS Departure
- Vehicle leaves the queue
- Delay $= 6 - 4 = 2$ seconds
- Queue is empty
- Remove scheduled departure
- Updated event calendar:

| Event         | Time (s) |
| ------------- | -------- |
| NS arrival    | 9        |
| EW arrival    | 7        |
| NS departure  | $\infty$ |
| EW departure  | $\infty$ |
| Signal change | 10       |
> Next event: EW arrival at $t=7$

## Performance Measures
- average delay
- maximum queue length
- average queue length
- vehicles processed
- delay by direction
- signal utilization

## Take-Away
- The simulation engine never changed
	- more state variables
	- more event types
	- richer performance metrics
- Discrete-event simulation provides a general framework for modeling complex systems

