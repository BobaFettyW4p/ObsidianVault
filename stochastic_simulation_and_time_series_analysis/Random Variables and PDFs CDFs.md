# Review: Key Concepts
- Outcome
	- the result of a single trial of a random experiment
		- single observation that does not need to be further decomposed
	- the most basic possible result of an experiment
- Event
	- a set of outcomes from a random experiment
	- may consist of one outcome, multiple outcomes, or no outcomes at all
	- a subset of the sample space
- Key Differences
	- Granularity
		- an outcome is a single result of the experiment
		- an event can include any combination of outcomes from the sample space
	- Probability
		- can be assigned to both outcomes and events
			- the probability of an outcome is the likelihood of that specific result ocurring
			- the probability of an event is the sum of the probabilities of the individual outcomes that make up that even
	- Complexity
		- an outcome is a simple, indivisible result
			- an event can be more complex, including combinations such as unions, intersections, and complements of other events or outcomes
- Random Variable
	- a variable whose possible values are the numerical outcomes of a random experiment
		- can be discrete or continuous
		- has a governing PDF which represents the probability law for all possible outcomes
- Ex: Flipping two coins:
	- TT -> 1, HT ->2, TH -> 3, HH -> 4
	- random variable $X$ can take on the values of any of these outcomes
- Ex: Discrete Random Process: Rolling two dice
	- $S = \{2,3,4,\dots,12\}$ if we define experimental result as the sum of the two dice
	- $P(X = x_i) = p(x_i)$
		- probability of the random varialbe $X$ has the value $x_i$
			- $\Sigma_{i=1}^n P(x_i) = 1$
![[Pasted image 20260627165831.png]]

- point of clarification
	- a random variable is a function that maps each outcome of a random experiment to a numerical value
		- it does not represent an outcome itself but instead assigns a value to each outcome
	- an event can be defined in terms of the values taken by a random variable, representing a set of outcomes
		- the event $X>0$ corresponds to all outcomes for which the random variable $X$ assigns a value greater than 0

## Continuous Random Variables - Probability Density Functions
- random variable can take any value, not just discrete ones
- Ex. Annual snowfall in Chicago
	- $P(X) \neq P(x = X)$
	- $\int_CP(x)dx = p(x \in C)$
![[Pasted image 20260627170313.png]]
- can't interpret the height at any point as the probability
	- interpret the area under a length $C$ to calculate the probability of occurring in that range

![[Pasted image 20260627170435.png]]

- $\int_{-\infty}^{\infty} P(x) =1$


### Cumulative Distribution Function (CDF)
- gives same info as PDF, but is more convenient to work with
- gives the probability $x$ will be greater than or equal to a value
- CDF: $F(x) = P(x \leq X)$
	- Discrete: $F(x_N) = \Sigma_{i=1}^N p(x_i)$
	- Continuous: $\int_{-\infty}^x p(x)dx$ 
		- the CDF is a cumulative sum of the PDF
	- $p(x) = \frac{df}{dx}$

![[Pasted image 20260627170648.png]]
- the derivative of the CDF is the PDF

## Joint PDFs
- suppose $X$ and $Y$ are two random variables. Their "joint CDF" is defined as:
	- $F(x,y) = P(X \leq x, Y \leq y)$
- continuous: $\int \int_{A,B} p(x,y)dxdy = P(x \in A, y \in B)$
- discrete: $P(x,y) = P(X = x, Y = y)$

- Ex. Rolling two three-sided dice
	- $X$ - sum of two dice
	- $Y$ - absolute value of difference between two dice
- $S = \{(1,1), (1,2), (1,3), (2,2), (2,3), (3,3)\}$
	-     $\frac{1}{9}$      $\frac{2}{9}$       $\frac{2}{9}$       $\frac{1}{9}$       $\frac{2}{9}$      $\frac{1}{9}$   

- $X$: Event A: Sum = 4
- $Y$: Event B: $| diff | = 2$
	- What is $P(X = 4, Y = 2) = P(A \cap B)$
		- $P( \{(1,3), (2,2)\} \cap \{(1,3)\} = P\{(1,3)\} = \frac{2}{9}$
- random variable is some numerical encoding (function) of an event


## Marginal vs. Conditional Probability

- Joint Probability of baseball team
	- Day game- $P(lose) = 0.10$, $P(win) = 0.30$
	- Night Game - $P(lose) = 0.50)$, $P(win = 0.10)$
- what are the marginal probabilities?
	- $P(win) = 0.30 0.10 = 0.40$
	- $P(loss) = 0.10 + 0.50 = 0.60$
- What are the conditional probabilities?
	- $P(win | day) = \frac{P(win, day)}{P(day)} = \frac{0.3}{0.4} = 0.75$
	- $P(win|night) = \frac{P(win, night)}{P(night)} = \frac{0.1}{0.6} = 0.16$
- Let $X$ represent win status and $Y$ represent game time
	- Are $X$ and $Y$ independent?
		- the odds of winning at night is much lower, implies no