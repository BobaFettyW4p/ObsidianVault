# What Are We Trying to Do?
- how do we understand the relationship between several factors and one outcome?
- Ex.
	- What affects house prices?
	- What drives student performance?
	- What explains salary differences?
	- What influences mortality rate?

# One Variable Isn't Enough
- a single variable gives only a partial picture
	- Ex. house price vs. square footage
- what about location, number of bedrooms, age?
- we need a more flexible modelling capability

# Multiple Inputs, One Output
- in multiple linear regression, we assume the outcome variable is approximately a linear combination of the explanatory variables
- our goal is to find the coefficients of this combination that best describe the relationship
	- typically by minimizing squared error
- Each $X$ contributes to explaining $Y$
- Note: "Linear" means linear in the coefficients - you can still include non-linear functions of variables like $X^2$, $\log X$, interactions, etc.
### Toy Regression Examples
![[screenshots/Pasted image 20260705181758.png]]

### What Do We Mean by Error?
- the *error* is the difference between the actual observed value and the predicted value from the model
	- that is: $error = y - \hat{y}$ 
- in least squares regression, we choose coefficients that minimize the sum of squared errors across all data points
	- why squares?
		- it makes the math tractable and rewards smaller, balanced errors
- Under reasonable assumptions
	- linear model, constant variance, uncorrelated errors
	- the least squares estimator is *unbiased*
	- it has the *smallest variance* among all linear unbiased estimators
		- Gauss-Markov theorem

#### Meaning of Unbiased
- regression assumes the existence of a true model:
$$y = X \beta + \epsilon$$
where $\beta$ is fixed but unknown
- each time we sample new data, we get a new estimate $\hat{\beta}$
- $\hat{\beta}$ is a random variable - it varies from sample to sample
- if $E[\hat{\beta}] = \beta$, then we say $\hat{\beta}$ is an unbiased estimator
- Intuition: over many samples, our estimates will "hover around" the true value

#### Why Minimize Variance?
- among all linear unbiased estimators, the least squares estimator has the lowest variance
- this means that across all repeated samples, $\hat{\beta}$ values from least squares will fluctuate less around the true $\beta$
- lower variance implies more stable and reliable estimates
	- in noisy data settings, this helps avoid overreacting to sample-specific quirks
		- known as the Guass-Markov theorem
- Intuition: even if two estimators are unbiased, the one with tighter spread is more untrustworthy
#### What Can Multiple Regression Tell Us?
- which variables matter?
- how much each variable contributes
- predictions based on what we know
- adjustment for confounding variables
- Flexibility: You can regress on any linear combination of any function of the variables - polynomials, interactions, even indicators

##### Real-World Examples
- Housing
	- What determines price?
- Health
	- What influences blood pressure?
- Education
	- What predicts GPA?
- Marketing
	- What drives sales?
- Sports
	- What boosts performance?

### Which Model is Better?
- use performance on unseen data (cross-validation, test sets) to judge
- good models strike a balance
	- explain enough variation
	- avoid ovefitting
	- are simple enough to interpret
- Metris to compare models
	- $R^2$, adjusted $R^2$
	- AIC/BIC (penalize complexity)
	- RMSE on validation data
- Bonus tip: When in doubt, simplify

### Is This Just Trial and Error?
- Model selection can feel uncertain - there are many ways to choose variables and functional forms
- but it's not arbitrary:
	- use domain knowledge to justify variable inclusion
	- use diagnostics (e.g. residuals, $R^2$, VIF) to assess fit and validity
	- Compare models with tools like cross-validation, AIC/BIC, adjusted $R^2$
	- Prefer simpler models when possible (Occam's Razor)
- Model building is iterative, but guided by evidence and reasoning, not really guesswork

##### Analogy: Fair Comparison
- does running improve health?
- or do runners also sleep more and eat better?
- regression helps isolate effects by adjusting for other factors

#### How to Think About It
- regression is about discovering patterns
- helps with:
	- interpretation
	- prediction
	- controlling for other variables
- not proof of causality - but useful reasoning
- more flexible than it looks:
	- not limited to raw variables
		- can use transformatios, combinations, and interactions
### Multiple Regression
- a principle goal in time series analysis is to explain one time series (the *output variable*) as a function of other time series (*input variables*)
- this process can be used to forecase future values of the output time series, or simply to deduce causality and thereby make decisions on how to manipulate a system
- a simple but deceptively powerful technique of doing such data modeling is referred to as *multiple linear regression*
- linear doe not imply a linear relationship between inputs and outputs but rather a linear combination of arbitrarily complex (often non-linear) functions of the inputs
- Specifically, a multiple regression model is described by:
$$x_1 = \beta_1z_{11} + \beta_2 z_{12} + \dots + \beta_q z_{1q} + w_1$$
$$x_2 = \beta_1 z_{21} + \beta_2z_{22} + \dots + \beta_qz_{2q} + w_2$$
$$\dots$$
$$x_n = \beta_1 z_{n1} + \beta_2 z_{n2} + \dots + \beta_q z_{nq} + w_n$$
- where:
- $x_t$, $t=1 \dots n$ are the response variables
- $z_{tj}, t = 1 \dots n, j = 1 \dots q$ are the inputs
- $\beta_j, j=1 \dots q$ are the regression coefficients
- $w_t, t = 1 \dots n$ are the errors
	- and error is anything that departs from our model
		- we assume for now that errors iid with mean zero and variance $\sigma_w^2$
### Matrix Form
- one way to consider the model:
	- there are $n$ observations
		- for each observation, there are $q$ observables
			- $n$ white noise terms represent our uncertainty about the value for each observation
- using matrix notation, the model can be simply expressed as:
$$x = Z \beta + w$$
 -where $x$ and $w$ are $n \times 1$ matrices, $\beta$ is a $q \times 1$ matrix, and $Z$ is an $n \times q$ matrix
- Example: Fit a linear time model to the global temperature time series given in Shumway
$$x_t = \beta_1 + \beta_2 t + w_t,\ t = 1880, \dots, 2009$$
- In this case, $q=2$, $z_{t1} = 1$, and $z_{t2} = t$
- the values of $\beta$ are found by solving an optimization problem
	- e.g. least squares
- note that the $\beta$ themselves are random variables since they are obtained from a sample process
	- we do not have the entire population statistics of $x_t$ and the errors $w_t$ are unknown
- what we seek are estimators $\hat{\beta}$ with desireable properties
- we consider a less trivial example once we study how $\hat{\beta}$ is obtained