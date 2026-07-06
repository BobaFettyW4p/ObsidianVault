- the model represents unknown population values for $\beta$
- since we do not know the white noise terms $w$, we cannot deterministically solve for $\beta$
- hence, we will derive estimates, denoted by $\hat{\beta}$, by minimizing some criterion using sample statistics
- a classic criterion is to find the least-squares fit that minimizes $S$:
$$S = (x - Z\hat{\beta})^T (x - Z \hat{\beta})$$
- note that a different sample from the population would yield a similar but slightly different $\hat{\beta}$
	- in this case, $\hat{\beta}$ is then itself a random variable

#### Basic Matrix Properties
- we denote column vectors by lowercase (e.g. $x$), and matrices as uppercase (e.g. $A$)
- the following elementary matrix properties are used in our derivations:
$$\frac{\delta}{\delta x}(x^T y) = y$$
$$\frac{\delta}{\delta x} (x^T Ax) = 2Ax$$
- if A is symmetric
$$(AB)^T = B^TA^T$$

#### Statement of Normal Equations
- the least squared estimate $\hat{\beta}$ seeks to minimize:
$$Q = \sum_{t=1}^n w_t^2 = \sum_{t=1}^n(x_t - \beta^Tz_t)^2$$
- in the original matrix notation, this yields the normal equations:
$$\hat{\beta} = (Z^TZ)^{-1}Z^T x$$

#### Derivation of Normal Equations:
- We start with the equation for square error:
$$Q = \sum_{t=1}^n w_t^2 = \sum_{t=1}^n (x_t - \beta^T z_t)^2$$
$$=(x - Z\hat{\beta})^T(x-Z\hat{\beta})$$
$$=x^Tx - x^T Z \hat{\beta} - \hat{\beta} Z^T x + \hat{\beta}^TZ^TZ\hat{\beta}$$
- then we minimize with respect to $\hat{\beta}$
$$\frac{\delta Q}{\delta \hat{\beta}} = 0 = -Z^T x - Z^T x + 2 Z^T Z \hat{\beta}$$
$$Z^TZ\hat{\beta} = Z^T x$$
$$\hat{\beta} = (Z^TZ)^{-1} Z^T x$$
- this result is referred to as the *normal equations*
- a few key points
	- this equation is extremely general
		- only linear in the coefficients $\hat{\beta}$
			- the inputs $Z$ can be arbitrary functions as in previous examples
	- least squares is just one model for finding the coefficients $\beta$ but is easy and has certain pleasing properties that are outline on the next slides
- note that the $\hat{\beta}$ themselves are random variables, estimators for $\beta$, since they are obtained with sample data
- Consider a less trivial example, where $M_t$ denotes cardiovascular mortality, $T_t$ denotes temperature and $P_t$ denotes the particulate levels:
$$M_t = \beta_1 + \beta_2 t + w_t$$
$$M_t = \beta_1 + \beta_2 t + \beta_3 (T_t - \bar{T}) + w_t$$
$$M_t = \beta_1 + \beta_2 t + \beta_3(T_t - \bar{T}) + \beta_4(T_t - \bar{T})^2 + w_t$$
$$M_t = \beta_1 + \beta_2 t + \beta_3(T_t - \bar{T}) + \beta_4 (T_t - \bar{T})^2 + \beta_5 P_t + w_t$$
- these represent a sequence of increasingly complex models for the mortality rate in terms of temperature and particulate levels
	- note that respectively we have:
$$z_{t,1:q} = (1,t), (1, t, T_t), (1, t, T_t, T_t^2), (1, t, T_t, T_t^2, P_t)$$

#### $\hat{\beta}$ as Unbiased Estimator
- Recall the normal equations:
$$\hat{\beta} = (Z_T Z)^{-1} Z^T x$$
$$x = Z \beta + w$$
- substituting gives:
$$\hat{\beta} = (Z^TZ)^{-1} Z^T (Z\beta + w)$$
$$= (Z^T Z)^{-1} Z^TZ\beta + (Z^TZ)^{-1}Z^T w$$
$$=\beta+(Z^TZ)^{-1}Z^T w$$
- taking the expectation of each side yields
$$E[\hat{\beta}] = E[\beta] + E[(Z^TZ)^{-1}Z^T w]$$
- $E[\beta]$ is just $\beta$ because it is constant
- the second term can be written as $(Z^TZ)^{-1}Z^T E[w] = 0$ if we assume zero conditional mean of errors