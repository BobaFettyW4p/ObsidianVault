- bookkeeping
	- make up quiz 1 is available until midnight tonight
	- quiz 3 is between 8 and 830
		- 7 minutes
	- midterm two weeks from today
		- reviewed practice test during lecture
			- advice: will all be based on the homework problems
- can we build a stochastic model from a real time series?
	- Raw Data -> Find Structure -> Remove Structure -> ACF/PACF -> Estimate AR Model -> Validate

### ARMA Models: Big Picture
- we model the current observation as:
$$\text{Observation} = \text{Predictable Part} + \text{Innovation}$$
- A general $ARMA(p,q)$ model is:
$$x_t = \Phi_1 x_{t-1} + \dots + \Phi_p x_{t-p} + w_t + \Theta_1 w_{t-1} + \dots + \Theta_q w_{t-q}$$
- AR terms: dependence on previous observations
- MA terms: dependence on previous innovations
- $w_t$: new innocation, usually modeled as white noise
> In a simulator, the innovations are the random numbers you feed into the simulator. In real data, we don't know those random numbers, so we estimate them by subtracting off our best prediction.

## AR vs. MA: Two kinds of memory

- $AR(p)$
$$x_t = \Phi_1 x_{t-1} + \dots + \Phi_p x_{t-p} + w_t$$
- memory stored in past observations
- shocks propagate through the state
- ACF tails off
- PACF cuts off

- $MA(q)$
$$x_t = w_t + \Theta_1 w_{t-1} + \dots + \Theta_q w_{t-q}$$
- memory stored in past innovations
- shocks affect a finite number of times
- ACF cuts off
- PACF tails off
> ARMA = AR memory + MA shock memory

## Stationarity and Invertibility: Review
- recall from last lecture:
	- for an AR(p) process:
$$x_t = \Phi_1 x_{t-1} + \dots + \Phi_[ x_{t-p} + w_t$$
- stationarity is determined by the roots of the characteristic equation:
$$1 - \Phi_1 z - \Phi_2 z^2 - \dots - \Phi_p z^p = 0$$
> All AR roots must lie outside the unit circle

- for MA terms, invertibility is handled similarly using the roots of the MA polynomial

## How We Identify a Candidate Model
- the ACF and PACF give diagnostic clues
- AR(p)  has an ACF that tails off and a PACF that cuts off after $p$
- MA(q) has an ACF that cuts off after $q$ and a PACF that tails off
- ARMA(p,q) has an ACF that tails off and a PACF that tails off

 - estimate average demand at each hour by averagign the stack of daily plots
$$x_t = d_t + r_t$$
- $d_t$ = the deterministic daily profile
- $r_t$ = the stochastic residual process

 - if the model is good, the innovations should resemble white noise
  