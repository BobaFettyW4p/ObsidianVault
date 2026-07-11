# Multiple Linear Regression
- we will go through regression and then auto-regressive processes
- underlying mathematical formalism
	- better presented in a recorded video
- we will talk more about what we're trying to accomplish, and then ujse the video to present the formalism
- many ways to fit data points to a line
	- least squares
		- outliers are weighted further
- multiple linear regression
	- multiple explanatory variables
		- some of one variable may be explained by a different explanatory variable

### What are we trying to do?
- how do we understand the relationship between several factors and one outcome?
- Ex.
	- what affects house prices?
	- what drives student performance?
	- what explains salary differences?
	- what influences mortality rate?

#### One Variable isn't enough
- a single variable gives only a partial picture
	- ex. home price vs. square footage
- what about location, number of bathrooms, age?
- we need a more flexible modeling capacity

### Multiple Inputs, One Output
- in multiple linear regression, we assume the outcome variable is approximately a linear combination of the explanatory cariables
- goal is to find the coefficients of this combination that best describe the relationship, typically by minimizing square error
- each X contributes to explaining Y
- Note: "Linear" means linear in the coefficients, cna still include non-linear functions of variables like $X^2$, $\log x$, interactions, etc.
### What do we Mean by Error?
- the *error* is the difference between the actual observed value and the predicted value from the model
	- e.g. $error = y - \hat{y}$
- in least squares regression, we choose coefficients that minimize the sum of squared errors across all data points
- why squares?
	- i makes the math tractable and rewards smaller, balanced errors
- under reasonable assumptions:
	- the least squares estimator is unbiased
	- it has the smallest variance among all linear unbiased estimators
		- Gauss-Markov

#### Meaning of "Unbiased"
- regression assumes the existence of a true model:
$$y = X \beta + \epsilon$$
- where $\beta$ is fixed but unknown
- each time we sample new data, we get a new estimate $\hat{\beta}$
- $\hat{\beta}$ is a random variable - it varies from sample to sample
- If $E[\hat{\beta}]$, then we say $\hat{\beta}$ is an unbiased estimator
- Intuition: Over many samples, our estimates will "hover around" the true value

### Why Minimize Variance?
- among all linear unbiased estimators, the least squares estimator has the lowest variance
- This means that across repeated samples, $\hat{\beta}$ values from least squares will fluctuate less around the true $\beta$
- lower variance implies more stable and reliable estimates
- in noisy data settings, this helps avoid overreacting to sample-specific quirks

### Which Model Is Better?
- use performance on unseen data to judge

### Is AR a good model for a given data set?
$$X_t = \Phi_1 x_t - 1 + \dots + \Phi_p x_{t-p} + w_t$$

- a moving average model of order $q$ is called $MA(q)$ and is defined as:
$$x_t = w_t + \Theta_1 w_{t-1} + \Theta_2w_{t-2} + \dots