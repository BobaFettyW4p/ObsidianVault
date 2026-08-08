- ARMA(p,q) models combine AR(p) and MA(q) models:
$$\Phi(B) x_t = \Theta(B) w_t$$
- where $\Phi$ and $\Theta$ are the usual AR and MA operators, respectively
- We would like to be able to write this as a one sided process in either $x_t$ or $w_t$ for analysis purposes
$$x_t = \frac{\Theta(B)}{\Phi(B)} w_t$$
$$w_t = \frac{\Phi(B)}{\Theta(B)} x_t$$
- the first inversion requires the roots of $\Phi(z)$ to lie outside the unit circle
- the second inversion requires the roots of $\Theta(z)$ to lie outside the unit circle

## ARMA Models - parameter redundancy
- be careful to check and ARMA model isn't overparametized
	- Operator notation is very useful
- Consider the AR(1,1) model:
$$x_t = 0.5 x_{t-1} +- 0.5 w_{t-1} + w_t$$
- This appears to be ARMA(1,1) with $\Theta_1 = \Phi_1 = 0.5$
- factor reveals it is the equivalent of white noise:
$$(1- 0.5B)x_t = (1- 0.5 B)w_t$$
$$x_t = w_t$$
### Parameter Restriction Summary
- to summarize, the following parameter restrictions exist for an ARMA(p,q) model:
	- redundancy
		- use operator notation and factor to ensure models are not overparameterized
	- non-stationarity or equivalently non-causality
		- for AR operator, ensure roots of AR polynomials lie outside the unit circle
			- equivalent of ensure the AR operator can be inverted
	- non-uniqueness
		- for an MA process, choose the representation where roots of MA polynomial lie outside the unit circle
			- ensures invertibility of MA operator
- Remember, writing an ARMA model strictly as either an MA or AR process might be useful for different forms of analysis
	- invertibility is an important property