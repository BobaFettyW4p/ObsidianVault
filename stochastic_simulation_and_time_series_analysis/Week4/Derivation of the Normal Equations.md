Recall:
$$X = Z \beta + w$$
- where X is the output time series written as a column vector
- $Z$ is the input time series
- $w$ is what we refer to as the *error*
- we want to find the regression coefficients $\beta$ such that we find the best fit between $Z$ and the output time series $X$

- we call this sum of squares difference $S$
$$S = (X-Z \beta)^T (X-Z \beta)$$
- the first term of this is:
$$(x_1 - \text{model value}_1)^2 +$$
$$(x_2 - \text{model value}_2)^2 +$$
$$(x_n - \text{model value}_n)^2$$
- find all $n$ $\beta$ such that $S$ is minimized
$$S = x^T x - x^T Z \beta - \beta^T Z^T x + \beta^T Z^T Z \beta$$
- all of these $\beta$ are $\hat{\beta}$
	- they're predictors
- each expression is a scalar even if it's not composed of scalars

$$\frac{\delta S}{\delta \beta} = 0$$
- used to find the least squares fit
- we'll need to differentiate term by term and use our properties from [[Matrix Properties for Regression]] in order to help us do this
$$\frac{\delta S}{\delta \beta} = \frac{\delta (X^T X)}{\delta \beta} = 0$$
- this one is easy because $X^T X$ doesn't depend on $\beta$ at all
$$X^T Z \beta = (Z \beta)^T X$$
- why is this?
	- $Z \beta$ is a vector, $X$ is a vector, we can do the dot product in either order
- this can expanded using a property from the first segment:
$$= \beta^T Z^T X$$
- next part:
$$\frac{\delta}{\delta \beta}\beta^T Z^T X$$
- this has the exact form of another property from the first lecture. Recall:
$$\frac{\delta}{\delta x} X^T y = y$$
- this enables us to simplify to:
$$ = Z^T X$$
- to consider the next term:
$$ - \frac{\delta}{\delta \beta} \beta^T Z^T X = -Z^T X$$
- and the next:
$$+ \frac{\delta}{\delta \beta} \beta^T Z^T Z \beta$$
- thus:
$$0 = -2 Z^T X + 2 Z^T Z \beta$$
$$Z^T X = Z^T Z \beta$$
- to solve, we need to invert the matrix:
$$\hat{\beta} = (Z^T Z)^{-1} Z^T X$$
- this is the equation we were looking for
	- called the *normal equations*
	- used to find $\hat{\beta}$
- the beta hat that minimizes the difference between the model and the error are found via the normal equations
