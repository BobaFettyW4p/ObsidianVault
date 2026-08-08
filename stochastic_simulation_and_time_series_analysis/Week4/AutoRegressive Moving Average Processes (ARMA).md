## Tiem Series Forecasting: The Big Goal
- understand how time-dependent variables evolve
- predict future values: forecasting
- detect underlying structure
	- trend
	- seasonality
	- memory/persistence
- separate signal from noise
- build compact, interpretable models to inform decisions

![[screenshots/Pasted image 20260711162552.png]]

## Time Series Forecasting as Conditional Simulation
- ARMA = Structured Random Process
	- AR: Future value depends linearly on past values
	- MA: Adds random shocks with short-term memory
> encodes both structure and uncertainty
- Forecasting = stochastic simulation
	- predicts future by sampling from conditional distributions
	- matches core idea of simulating outcomes under uncertainty
- Discrete-Event Models as Analogy
	- like queues: next state depends on prior state + random input
	- time series: squential, feedback-driven uncertainty
- Probability and Computation
	- conditional distributions -> simulation engines
	- forecasting automates inference over time
> Key Insight: Forecasting is simulation - conditioned on past - with temporal structure and uncertainty
### Where ARMA Fits In
- ARMA models are classical, compact models for capturing temporal structure
- Combine two key ideas:
	- AR (*AutoRegressive*): leverages autocorrelation in past values to forecast the future
	- MA (*Moving Average*): describes how recent shocks affect the current value once the model identifies waht those shocks were
- require stationarity, or preprocessing that results in a roughly stationary state
- often serve as a diagnostic, benchmark, or residual models, but can also be valuable tools for direct analysis and forecasting in their own right when assumptions are met
## Goals and Limits of ARMA
- Strengths:
	- efficient for short-memory, stationary processes
	- Interpretable: coefficients have clear roles
	- Statistically well-understood: estimation, inference, diagnostics
	- performs well in many forecasting tasks
- Limitations:
	- assumes stationarity, constant mean/variance over time
	- only captures linear dependence
	- not suitable for regime shifts or structural breaks
	- doesn't directly handle trends or seasonality
### Success Stories for ARMA Models
- electricity demand forecasting: short-term prediction using past patterns and weather-adjusted residuals
- Residual modeling in seasonal decomposition to capture non-seasonal dynamics
	- e.g. STL + ARMA
- hydrological flows and rainfall modeling
	- short memory and noise handling support water management planning
- financial return modeling
	- ARMA + GARCH
	- ARMA captures linear autocorrelation, GARCH models volatility clustering
- diagnostic modeling:
	- uncover autocorrelation structure to inform model selection and improve residual analysis
- control systems and engineering
	- model noise and feedback behavior in physical systems
> Note: ARMA often shines when preprocessing is effective. Detrending and deseasonalizing improve model fit

## ARMA vs. Other Forecasting Approaches

| Method         | Stengths                   | Weaknesses                             |
| -------------- | -------------------------- | -------------------------------------- |
| ARMA           | Interpretable, fast        | Stationary linear only                 |
| ARIMA/SARIMA   | Handles trend/seasonality  | More parameters, still linear          |
| Exp. Smoothing | Simple, good short-term    | No residual structure handling         |
| Neural Nets    | Nonlinear, powerful        | Requires large data, hard to interpret |
| State-Space    | Handles missing/noisy data | Implementation complexity              |
## Step 1: Establishing Stationarity
- a series is stationary if mean, variance, and covariance remain constant over time
- AR assumptions require stationarity
	- non-stationarity can bias estimates
- Common non-stationary sources
	- trends (deterministic or stochastic)
	- unit roots
	- seasonality
- Test: ADF, KPSS, Phillips-Perron
- Remedies include differencing, detrending and seasonal adjustment

## Step 2: Is AR a Good Model for a Given Data Set?
- AR model implies:
$$x_t = \Phi_1 x_{t-1} + \dots + \Phi_p x_{t-p} + w_t$$
- where shocks $\epsilon_t$ are white noise
- Indicators of AR-fit:
	- autocorrelation function (ACF) decays gradually
	- Partial autocorrelation (PACF) cuts off after lag $p$
	- No visible strong seasonality or exogenous patterns
- Diagnostics:
	- Plot ACF and PACF
	- Fit AR models and examine residuals for whiteness
	- use AIC or BIC for model comparison
## Step 3: Determining AR order (p)
- PACF heuristic: for AR(p), PACF drops to zero after lag $p$
- Model selection via:
	- AIC, BIC over a grid of candidates (e.g. $p=0, 1, \dots, 10)
	- cross-validation in a long time series
- Practical workflow:
	- fit candidate $AR(1) \dots AR(10)$
	- Compare AIC/BIC
	- Examing residuals for independence (white noise)
## Step 4: Estimating Coefficients and Forecasting
- $AR(p)$ model from:
$$x_t = \Phi_1 x_{t-1} + \dots + \Phi_p x_{t-p} + w_t$$
- Estimation methods:
	- YuleWalker equations (moment of moments)
	- Ordinary Least Squares (OLS)
	- Maximum Likelihood Estimation (MLE)
- Diagnostics:
	- Residual ACF, Portmanteau tests (e.g. LjungBox)
	- Check for homoscedasticity
- One-step-ahead forecast:
$$\hat{x}_{n+1} = \Phi_1 x_n + \Phi_2 x_{n-1} + \dots + \Phi_p x_{n-p+1}$$
- this assumes future errors $w_{n+1}, w_{n+2}, \dots$ are $0$ in expectation
