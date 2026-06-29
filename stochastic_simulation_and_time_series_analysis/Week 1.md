- stochastic computational methods with a focus on time series
	- not a statistics class
		- will bump into a lot of statistical concepts
	- not a probability class
		- will bump into a lot of probability

### Time Series Analysis and Stochastic Processes - two sides of the same coin
- two sides of the same coin
	- Stochastic Simulation
		- generating data from a model
	- Time Series Analysis
		- inferring data from a model
- the course focuses on both sides of this coin
## What does probability mean?
- Probability as Frequency
	- probability describes the behavior of an ensemble of repeated experiments
	- Ex.
		- $p$ of heads on a fair coin = 0.5
		- $p$ a manufactured part is defective = 0.002
		- $p$ a radioactive nucleus decays in the next hour
	- Strength:
		- closely tied to observable data
	- Weakness:
		- what is the probability that a startup succeeds?
		- what is the probability that a quantum computer reaches fault tolerance by 2035?
			- prof. Siegel's guess: 10%
- What exactly are the repeated experiments?
	- if you literally repeated the experiment, you'd get the same result
		- introduce variability through experiment design

## Probability as Information
- a common misconception:
	- "Probability is subjective"
- a stronger statement is:
	- "probability is conditional on information"
- Ex.
	- What is the probability that the next card in a shuffled deck is an Ace?
		- $p(Ace) = \frac{4}{52}$
- suppose I secretly look at the card
	- for me:
		- $p(Ace) = 1$ if I saw an Ace
		- $P(Ace) = 0$ is I did not
	- for you:
		- $p(Ace) = \frac{4}{52}$
- Who is correct?
	- both of us
- probability depends on the information available to the observer

## A Practical View
- Many real-world decisions combine both perspectives
- Ex.
	- What is the probability that a quantum-computing roadmap succeeds on schedule?
- We naturally ask:
	- what similar programs have succeeded?
	- how often have comparable efforts slipped?
	- what evidence exists today?
	- what technical risks remain?
- historical frequencies inform our bleiefs
- bayesian vs. frequentists view of probability
- the final answer is not a frequency
	- the final answer is a degree of belief informed by evidence
- Frequentist question:
	- "What can I learn from the ensemble?"
- Bayesian question:

