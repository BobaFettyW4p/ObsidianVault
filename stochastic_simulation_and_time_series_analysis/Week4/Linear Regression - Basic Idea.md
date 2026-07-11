## What are We Trying to Do?
- How do we understand the relationship between several factors and one outcome?
- Ex.
	- What affects house prices?
	- What drives student performance?
	- What explains salary differences?
	- What influences mortality rate?

### One Variable Isn't Enough
- a single variable gives only a partial picture
- Ex. House price vs. Square footage
- What about location, number of bedrooms, construction date?
- We need a more flexible modelling capacity

### Multiple Inputs, One Output
- in multiple linear regression, we assume the outcome variable is approximately a linear combination of the explanatory variables
- our goal is to find the coefficients of this combination that best describe the relationship, typically by minimizing square error
- each $X$ contributes to explaining $Y$
- Note: "Linear" means linear in the coefficients, can still include non-linear functions of variables like $X^2$, $\log X$, interactions, etc.

## Toy Regression Examples

![[Pasted image 20260711150632.png]]

### What Do  We Mean By Error?
- The *error* is the difference between the actual observed value and the predicted value from the model
	- e.g. $\text{error }= y - \hat{y}$
- In least squares regression, we choose coefficients that minimize the sum of squared errors across all data points
- Why squares? It makes the math tractable and rewards smaller, balanced errors
- Under reasonable assumptions (Linear model, constant variance, uncorrelated errors):
	- the least squares estimator is *unbiased*
	- It has the smallest variance among all linear unbiased estimators
		- *Gauss-Markov theorem*

## Meaning of "Unbiased"
- Regression assumes the existence of a true model:
$$ y = X \beta + \epsilon$$
- where $\beta$ is fixed but unknown
- each time we sample new data, we geta  new estimate $\hat{\beta}$
- $\hat{\beta}$ is a random variable - it varies from sample to sample
- If $E[\hat{\beta}] = \beta$, then we say $\hat{\beta}$ is an unbiased estimator
- Intuition: Over many samples, our estimates will "hover around" the true value

### Why Minimize Variance?
- Among all linear unbiased estimators, the least squares estiamtor has the lowest variance
	- this means that across repeated samples, $\hat{\beta}$ values from least squares will fluctuate less around the true $\beta$
- lower variance implies more stable and reliable estimates
- In noisy data settings, this helps avoid overreacting to sample-specific quirks
- This is what is known as the **Gauss-Markov theorem**
> Intuition: Even if two estimators are unbiased, the one with tighter spread is more trustworthy

### What Can Multiple Regression Tell Us?
- which variables matter?
- how much each variable contributes
- predictions based on what we know
- adjustment for confounding variables
- Flexibility
	- you can regress on any linear combination of any function of the variables - polynomials, interactions, even indicators

#### Real World Examples

| Domain    | Question                        |
| --------- | ------------------------------- |
| Housing   | What determines price?          |
| Health    | What influences blood pressure? |
| Education | What predicts GPA?              |
| Marketing | What drives sales?              |
| Sports    | What boosts performance?        |

### Which model is better?
- Use performance on unseen data (cross-validation, test sets) to judge
- good models strike a balance
	- explain enough variation
	- avoid overfitting
	- are simple enough to interpret
- Metrics to compare models:
	- $R^2$, adjusted $R^2$
	- AIC/BIC (penalize complexity)
	- RMSE on validation data
> Tip: When in doubt, simplify

### Is This Just Trial and Error?
- Model selection can feel uncertain
	- there are many ways to choose variables and functional forms
- But it is not arbitrary
	- use domain knowledge to justify variable inclusion
	- use diagnostics (e.g., residuals, $R^2$, VIF) to assess fit and validity
	- compare models with tools like cross-validation, AIC/BIC, adjusted $R^2$
	- prefer simpler models when possible
- model building is not iterative, but guided by evidence and reasoning, not really guesswork
#### Analogy: Fair Comparison
- does running improve health?
	- or do runners also sleep more and eat better?
- are Toyotas more durable than other models?
	- or do Toyota owners tend to take better care of their cars?
- Regression helps isolate effects by adjusting for other factors

#### How To Think About It
- Regression is about discovering patterns
- Helps with:
	- Interpretation
	- Prediction
	- Controlling for other variables
- Not proof of causality, but useful reasoning
- More flexible than it looks: you're not limited to raw variables, can use transformations, combinations, and interactions