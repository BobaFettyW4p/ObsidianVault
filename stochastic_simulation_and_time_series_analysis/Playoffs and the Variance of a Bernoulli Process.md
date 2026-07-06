 - variance
	 - the closer in probability two outcomes are, the more trials you need to establish the difference betweent hem
> "If Team Blue has probability $p$ of defeating Team Red in each independent game, when is the outcome most variable?"

- model one game as a Bernoulli trial
$$
X = 1 \text{ if Blue wins}, 0 \text{ otherwise}
$$

$$
P(X=1) = p
$$

- Variance is spread around the mean:
$$
Var(X) = E[(X- \mu)^2], \mu = E[X]
$$
	- variance is the average squared distance from the mean
	- even better: standard deviation is the RMS distance from the mean:
$$
\sigma = \sqrt{Var(X)}
$$
- in the case where one team is a strong favorite, more outcomes lie close to the mean
![[screenshots/Pasted image 20260620115412.png]]

- in a case where both teams are evenly matched, every observation is very far from the mean
![[screenshots/Pasted image 20260620115441.png]]
- in this matchup, every outcome is 0.5 away from the mean
	- the variance is maximized in this case
- The formula:
$$
Var(X) = p(1-p)
$$

$$
E[x] = p
$$
$$
E[X^2] = p
$$
$$
Var(X) = E[X^2] - E[X]^2
$$
$$
= p-p^2 = p(1-p)
$$
- Let X be the number of Blue wins in an $n$-game series
$$
X \cong Binomial(n,p)
$$
$$
E[X] = np
$$
$$
Var(X) = np(1-p)
$$
- we usually care about the win fraction
$$
p = X/n
$$
$$
E[p] = p
$$
$$
Var(p) = \frac{p(1-p)}{n}
$$

- key point: long series reduce noise by a factor of $\sqrt{n}$ 
	- close teams need many more games to differentiate themselves
$$
signal = n(p-0.5)
$$
$$
noise = \sqrt{np(1-p)}
$$

- variance is not the same as "how often blue wins"
	- a Bernoulli outcome is either 0 or 1, but it's mean is $p$
	- Variance measures the average squared distance from that mean
	- $p(1-p)$ is largest at $p=0.5$
		- the least predictable one game outcome
	- In a series, averaging reduces noise, but close matchups need longer series
> The closer teams are, the more games you need before the better team reliably reveals itself