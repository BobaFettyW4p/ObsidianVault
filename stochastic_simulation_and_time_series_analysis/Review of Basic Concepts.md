### Ex: 7 Horse Race
- Sample space S = {all orderings of (1..7)}
- $|S| = 7!$
- Example events:
	- E1: {Horse 4 wins}
- the total number of events is a subset of the total number of outcomes
	- for our horse race, $2^{7!} = 2^{5040}$
### Definitions
- For any events A and B
	- $A \union B$ - all outcomes that in either A,B, or both
	- $AB$ - A intersect B, all outcomes in both A and B
	- $AB = \null$, A,B are disjoint
	- $A^c$ occurs if $A$ does not

### Axioms of Probability
- the probability of an event has to be between 0 and 1
	- $0 \leq P(A) \leq 1$
- Some event in the sample space has to occur: $P(S) = 1$
- $P(P \union B) = P(A)+P(B)\ \for P(AB) = 0$
	- P(Ace)  \frac{1}{13}
	- $P(King) = \frac{1}{13}$
	- $P(A \Union K) = P(A) + P(K) = \frac{2}{13}$ since $p(AK) = 0$

#### Conditional Probability
- "Probability of A given B"
- $P(A|B) = \frac{P(AB)}{P(B)}$
	- where A, B are any two events
- what info does this give you?