- now that we have derived the normal equations, let's talk about how to solve
- to review:
$$\hat{\beta} = (Z^T Z)^-1 Z^T X$$
$$(Z^T Z) \hat{\beta} = Z^T X$$
- very intensive to solve by hand, do it on a computer
- can represent this as:
	- $(Z^T Z) = A
	- $\hat{\beta}$ = $x$
	- $Z^T \bar{X}$ = $b$
- giving us:
$$Ax = b$$
- can be solved in Matlab with $\texttt{A/b}$
	- or in python with $\texttt{np.linalg.solve(A,b)}$
	- may use a variety of techniques, but generally uses Gaussian elimination
- dataset is collected from Shumway
	- 3 times series
		- output time series: $M_t$ - mortality data
		- 2 input times series:
			- $T_t$ - temperature data
			- $P_t$  - particulate density
- we need to hypothesize a linear relationship between some combination of the inputs and the outputs
- Case 1:
	- the mortality data is a function of time only
$$M_t = \beta_1 + \beta_2 t + w_t$$
- Case 2:
	- the mortality data is linear in both time and temperature fluctuations
$$M_t = \beta_1 + \beta_2 t + \beta_3(T
_t - \bar{T}) + w_t$$
- Case 3:
	- there is a quadratic relationship with temperature fluctuations
$$M_t = \beta_1 + \beta_2 t + \beta_3 (T_t - \bar{T}) + \beta_3(T_t - \bar{T})^2 + w_t$$
- Case 4:
	- finally, there is a linear relationship with particulate density in addition to Case 3
$$M_t = \beta_1 + \beta_2 t + \beta(T_t - \bar{T}_ + \beta_4 (T_t - \bar{T})^2 + \beta_5 P_t + w_t$$

- want to evaluate all cases, and find which case has the best fit
	- minimizes least squares difference