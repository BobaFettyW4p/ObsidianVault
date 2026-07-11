### Time Series - Multiple Regression
- a principle goal in time series analysis is to explain one time series (the *output variable*) as a function of other time series (*input variables*)
- this process can be used to forecast future values of the output time series, or simply to deduce causality and thereby make decisions on how to manipulate a system
- a simple but deceptively powerful technique of doing such data modeling is referred to as *multiple linear regression*
- Linear in this case does not imply a lienar relationship between inputs and outputs but rather a linear combination of arbitrarily complex (often non-linear) functions of the inputs
- Specifically, a multiple regression model is described by:
$$x_1 = \beta_1 z_{1 1} + \beta_2 z_{1 2} + \dots + \beta_q z_{1 q} + w_1$$
$$x_2 = \beta_1 z_{2 1} + \beta_2 z_{2 2} + \dots + \beta_q z_{2 q} + w_2$$
$$\dots$$
$$x_n = \beta_1 z_{n 1} + \beta_2 z_{n 2} + \dots + \beta_q z_{n q} + w_n$$
- where
	- $x_t, t = 1 \dots n$ are the *response* variables
	- $z_{tj}, t = 1 \dots n$, $j = 1 \dots q$ are the *inputs*
	- $\beta_j, j = 1 \dots q$ are the regression coefficients
	- and $w_t, t = 1 \dots n$ are the *errors*
		- errors represent anything that departs from our model
			- for now, we assume errors are IID with mean zero and variance $\sigma_w^2$

### Matrix Form
- One way to consider the model:
	- there are $n$ observations
		- for each observation, there are $q$ observables
	- the $n$ white noise terms represent our uncertainty about the value for each observation
- Using matrix notation, the model can be simply expressed as:
$$x = Z \beta + w$$
- where $x$ and $w$ are $n \times 1$ matrices (column vectors)
- $\beta$ is a $q \times 1$ matrix (column vector)
- $Z$ is an $n \times q$ matrix

## Example
- fit a linear time model to the global temperature time series give in Shumway
$$x_t = \beta_1 + \beta_2 t + w_t, t = 1880, \dots, 2009$$
- in this case $q = 2, z_{t1} = 1$, and $z_{t2} = t$
- the values of $\beta$ are found by solving an optimization problem
	- e.g. least squares
- Note that the $\beta$ themselves are random variables since they are obtained from a sample process
	- i.e. we do not have the entire population statistics of $x_t$ and the errors $w_t$ are unknown
- What we seek are estimators $\hat{\beta}$ with desirable properties
- we can consider a less trivial example once we study how $\hat{\beta}$ is obtained