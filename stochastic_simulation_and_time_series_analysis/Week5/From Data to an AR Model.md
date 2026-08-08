- The goal:
	- Can we build a stochastic model from a real time series?
		- Raw data -> Find Structure -> Remove Structure -> ACF/PACF -> Estimate AR Model -> Validate

## Step 1: Explore the Data

> What patterns are immediately visible?

![[screenshots/Pasted image 20260717201323.png]]

- Trend?
- Daily cycle?
- Noise?
- Anything unexpected?

### ARMA Models: Big Picture
- We model the current observation as:
$$\text{Observation} = \text{Predictable part} + \text{Innovation}$$
- A general $ARMA(p,q)$ model is:
$$x_t = \Phi_1 x_{t-1} + \dots + \Phi_p x_{t-p} + w_t + \Theta_1 w_{t-1} + \dots + \Phi_q w_{t-q}$$
	- AR terms: dependence on previous observations
	- MA terms: dependence on previous innovations
	- $w_t$: new innovation, usually modelled as white noise
> In a simulator, the innovations are the random numbers you feed into the simulator. In real we data, we don't know those random numbers, so we estimate them by subtracting off our bets prediction.

### AR vs MA: Two Kinds of Memory
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
$$ARMA = \text{AR memory} = \text{MA shock memory}$$
### Stationarity and Invertibility: Review
- For an $AR(p)$ process:
$$x_t = \Phi_1 x_{t-1} + \dots + \Phi_p x_{t-p} + w_t$$
- stationarity is determined by the roots of the characteristic equation:
$$1 - \Phi_q z - \Phi_2 z^2 - \dots - \Phi_p z^p = 0$$
> all AR roots must lie outside the unit circle

- for MA terms, invertibility is handled similarly using the roots of the MA polynomial

### How We Identify a Candidate Model

- the ACF and PACF give diagnostic clues


| Model       | ACF                | PACF               |
| ----------- | ------------------ | ------------------ |
| AR($p$)     | tails off          | cuts off after $p$ |
| MA($q$)     | cuts off after $q$ | tails off          |
| ARMA($p,q$) | tails off          | tails off          |
- these are clues, not guarantees
- real data are rarely textebook-perfect
- after fitting, we check whether the innovations look like white noise

## Step 2: Look for Deterministic Structure
> Stack the daily plots. Does each day have a similar shape?

![[screenshots/Pasted image 20260717202857.png]]

> What repeats? What differs? Weekdays vs. weekends?

## Estimate the Typical Day

- estimate the average demand at each hour by averaging the stack of daily plots:
$$x_t = d_t + r_t$$
- $d_t$: deterministic daily profile
- $r_t$: stochastic residual process
![[screenshots/Pasted image 20260717203012.png]]

## Step 4: Remove the Daily Pattern
- subtract the estimated daily profile:
$$r_t = x_t - d_t$$
![[screenshots/Pasted image 20260717203200.png]]
> What structure remains?
## Step 5: Examine the Correlation Structure
![[screenshots/Pasted image 20260717203326.png]]

## Step 6: Choose the AR Order

![[screenshots/Pasted image 20260717203357.png]]

> How many significant lags do you see?
> What value of $p$ would you choose?

## Step 7: Estimate the Coefficients
- Assume an $AR(p)$ model:
$$x_t = \Phi_1 x_{t-1} + \Phi_2 x_{t-2} + \dots + \Phi_p x_{t-p} + w_t$$
- estimate $\Phi$ using the Yule-Walker equations:
$$R \Phi = r$$
![[screenshots/Pasted image 20260717203618.png]]

## Our Fitted AR(2) Model
- estimated model for the residual demand:
$$\hat{x_t} = 1.4487 x_{t-1} - 0.4984 x_{t-2}$$
- How is the prediction made?
	- Use the two most recent observations
	- Multiply by the estimated coefficients
	- Sum the results
	- Do not include the innovation term
- The true process is:
$$x_t = 1.4487 x_{t-1} - 0.4984 x_{t-2} + w_t$$
- before the next observation is collection, the innovation $w_t$ is unknown
- Since $E[w_t] = 0$, the bets one step ahead prediction is obtained by setting

## Step 8: Validate the Model
- estimated innovations:
$$\hat{w_t} = x_t - \sum_{i=1}^p \hat{\Phi} x_{t-i}$$
- if the model is good, the innovations should resemble white noise:
![[screenshots/Pasted image 20260717204058.png]]

## What Can We Do Now?
- we have replaced hundreds of observations with:
$$\text{Typical Day} + \text{AR Model} + \text{Innovatins}$$
- With the fitted model, we can:
	- forecast future observations
	- simulate realistic synthetic time series
	- understand persistence and memory
	- quantify uncertainty using the innovation variance
	- compare competing models: AR, MA, ARMA, ARIMA

## Take-Home Message
- Look at the data
- Explain what you can
- Model what you cannot determine deterministically
- Validate what remains.