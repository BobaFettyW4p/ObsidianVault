- given two bags:
	- Bag A: 700 red balls, 300 blue balls
	- Bag B: 300 red balls, 700 blue balls
- pick a bag randomly
- choose 12 balls without replacement
- if 8 red and 4 blue are chosen, what is the chance that bag A was chosen?

$$
P(A) = \text{probability bag A was selected} = 0.50
$$
$$
P(B) = \text{probability bag B was selected} = 0.50
$$
$$
\frac{P(A|C)}{P(B|C)} = \frac{P(C|A)\times P(A)}{P(C|B) \times P(B)}
$$
$$
P(C|A) = P(\text{k successes in n trials}) = \binom{n}{k} p^kq^{n-k}
$$
