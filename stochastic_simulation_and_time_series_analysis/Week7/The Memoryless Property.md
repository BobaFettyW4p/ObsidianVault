## Where We Are in the Story
- Part 1:
	- How many arrivals occur in a fixed interval?
$$N(t) \sim Poisson(\lambda t)$$
- Part 2:
	- How long until the next arrival
$$T \sim Exp(\lambda)$$
- Next:
	- why this waiting-time model is so unusual

![[Pasted image 20260801104202.png]]

## A Thought Experiment

- You have already waited *30 minutes* for the next bus
	- Should you expect the bus to arrive sooner than someone who just arrived?

![[Pasted image 20260801104240.png]]

### Two People, Same Stop
- Alice
	- arrived 30 minutes ago, still waiting
- Bob
	- arrives right now
>Who has the shorter expected remaining wait?


![[Pasted image 20260801104321.png]]

## The Surprise

- the *memoryless property*

> For exponential waiting times, the elapsed waiting time gives no information about the remaining waiting time

$$P(T > s + t | T > s) = P( T> t)$$

## What the Formula Says
- Let $T$ be the waiting time to the next event
- $T > s$: we have already waited $s$ unites
- $T > s+t$: we wait at least $t$ more units
- Memoryless says:
$$P(\text{wait at least t more} | \text{already waited s}) = P(T > t)$$

![[Pasted image 20260801111902.png]]


### Use the Survival Function
- For $T \sim Exp(\lambda)$:
$$P(T > x) = e^{-\lambda x}, x \geq 0$$

![[Pasted image 20260801113437.png]]

## Proof of Memorylessness
- Start with conditional probability:
$$P(T>s+t | T>s) = \frac{P(T>s+t \land T>s)}{P(T>s)}$$
- but, if $s,t \geq 0$, then $T > s+t$ automatically implies $T>s$, so:
$$P(T>s+t | T>s) = \frac{P(T>s+t)}{P(T>s)}$$
- Using $P(T>x) = e^{-\lambda x}$:
$$\frac{e^{-\lambda(s+t)}}{e^{-\lambda s}} = e^{-\lambda t} = P(T > t)$$

### Another Way to See It
![[Pasted image 20260801114323.png]]

- put simply:
> Conditioning on "nothing has happened yet" leaves the remaining exponential clock with the same distribution

## A Common Misinterpretation
- Memoryless does not mean that events have no history, or past events did not occur
- It means the conditional distribution of the remaining wait does not depend on the elapsed wait

## Why This is Special
> Among continuous distributions on $[0, \infty)$, the exponential distribution is the only one with the memoryless property

- Equivalently, if:
$$P(T> s+t | T >s ) = P(T > t), \text{ for all } s,t, \geq 0$$
- then the survival function must have the form:
$$P(T>t) = e^{-\lambda t}$$
- this is why memoryless is not just a curiosity, it characterizes the exponential distribution

## When Is Memoryless Reasonable?
- Often reasonable:
	- Poisson arrivals
	- radioactive decay
	- idealized packet arrivals
	- random failures with constant hazard
- Usually not reasonable
	- human lifetimes
	- aging machines
	- batteries
	- tires, bridges, appliances
- the exponential model assumes the instantaneous risk does not age

## Simulation Interpretation
- in a discrete event simulation, an exponential clock can be restarted after every event
![[Pasted image 20260801115518.png]]

$$T_1, T_2, T_3, \dots \sim^{i.i.d} Exp(\lambda)$$
## Key Takeaways
- the exponential distribution models waiting times between Posson arrivals
- Memoryless says elapsed waiting time does not affect the remaining wait
- Algebraically, the property follows from the exponential survival function
- Conceptually, the exponential clock always "starts over"