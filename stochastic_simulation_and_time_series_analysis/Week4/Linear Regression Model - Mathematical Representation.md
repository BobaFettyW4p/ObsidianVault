- we will write the equations that form the linear regression model
	- a little more subtle than it might appear
- imagine we have a time series $x_t, t=1,2,\dots, n$
	- a time series with $n$ time points
	- the *output*
- we want to ask what other processes might be independent variables that explain this time series
	- act as a dependent variable
- our output time series is our starting point
	- what we're hoping to get more insight into
		- will use other time series $y$, $z$
			- arrange them as a matrix
- to do this, we need to rephrase our notation for x:
	- $\bar{x}$ is the column vector of $n$ values of the output time series
	- $Z$ is the $n \times q$ matrix
		- $n$ rows, $q$ different *input* time series
$$Z = \begin{bmatrix}
z_{11} & z_{1 2} & z_{1q}\\
z_{21} & z_{22} & z_{2q}\\
\dots & \dots & \dots \\ 
z_{n1} &z_{n2} & z_{nq}
\end{bmatrix}$$
- a linear combination of the input time series will explain the output time series
	- can be a linear combination of any function
- $\beta$ represents the regression coefficients
	- this is what we need to solve for
	- need to find the best fit between the input time series and output time series
- $w$ represents "errors"
$$\bar{x} = Z \beta + w$$
 - this is the final set up of our system
	 - will give us $n$ equations, where $Z$ is an $n \times q$ matrix
	 - to look at these equations:
$$x_1 = Z_{11} \beta_1 + Z_{12} \beta_2 + \dots + Z_{1q} \beta_q + w_1$$
$$x_2 = Z_{21} \beta_1 + Z_{22} \beta_2 + \dots + Z_{2q}\beta_q + w_2$$
$$\dots$$
$$x_n = Z_{n1} \beta_1 + Z_{n2} \beta_2 + \dots + Z_{nq} \beta_q + w_q$$
- we know all $x$ and $Z$, but we do not know any $\beta$
	- we need to solve for all $\beta$ and $w$ values together using the entire matrix
		- there are tools to gauge how good a fit each is
- this just sets up the problem, we'll look at the system of equations to solve next