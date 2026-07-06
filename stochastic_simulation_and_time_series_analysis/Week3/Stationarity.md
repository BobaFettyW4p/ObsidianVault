- in practice, we usually only have one realization of a time series
- to estimate statistical quantities from a single realization, we need an additional assumption
	- this key assumption is stationarity
	- stationarity lets us treat different points in time as samples from the same statistical process
![[screenshots/Pasted image 20260705171009.png]]

#### Strict Stationarity
- a time series is *strictly stationary* if its probabilistic behavior is unchanged by a shift in time
- the joint distribution of $\{x_1, x_2, \dots, x_n\}$ is identical to that of $\{x_{1+h},x_{2+h}, \dots, x_{n+h}\}$ for every valid shift $h$
- consequently, the marginal distributions are identical at every time
![[screenshots/Pasted image 20260705171228.png]]

#### Weak Stationarity
- strict stationarity is often stronger than we need
- a weaker and more practical assumption is *weak stationarity*
- A weakly stationary process satisfies:
	- the mean is constant ($\mu_t = \mu$)
	- the autocovariance depends only on the lag ($\gamma(s,t) = \gamma(s+h,t+h)$)
		- equivalently, $\gamma(s,t) = \gamma(s-t)$
![[screenshots/Pasted image 20260705171450.png]]

#### Why Stationarity Matters
- for a stationary time series, the sample mean becomes:
$$\bar{x} = \frac{1}{n}\sum_{i=1}^n x_i$$
- if the samples are uncorrelated:
$$var(\bar{x}) = \frac{\sigma^2}{n}$$
- in time series, nearby samples are often correlated
- the variance of the sample mean therefore depends on the autocovariance function
![[screenshots/Pasted image 20260705171911.png]]

#### Estimation of Correlation
- when analyzing real data we typically do not have access to large ensembles and certainly do not know the PDFs
	- thus we have to estimate statistical quantities from sample data
- for a stationary time series the mean function is taken as constant and we can estimate it using the sample mean:
$$\bar{x} = \frac{1}{n}\sum_{t=1}^n x_t$$
#### Error in Sample Mean for Correlated Samples
- the standard error in the sample mean is the square root of $var(\bar{x})$

$$var(\bar{x}) = var(\frac{1}{n}\sum_{t=1}^n x_t)$$
$$=\frac{1}{n^2}cov(\sum^n+{t=1}x_t, \sum_{s=1}^n x_s)$$
$$=\frac{1}{n^2}(n \gamma_x(0) + (n-1) \gamma_x(1) + (n-2)\gamma_x(2) + \dots + \gamma_x(n-1)$$
$$= \frac{1}{n} \sum_{h = -n}^n(1 - \frac{| h|}{n}) \gamma_x(h)$$
- this will reduce to $\frac{\sigma_x^2}{n}$ in the case of independent samples

#### Estimation of Correlation
- the *sample autocovariance* function is defined as:
$$\hat{\gamma}(h) = \frac{1}{n} \sum_{t=1}^{n-h} (x_{t+h} - \bar{x})(x_t - \bar{x})$$
- with $\hat{\gamma}(-h) = \hat{\gamma}(h) = 0, 1, \dots, n-1$
- the *sample autocorrelation function* is defined as:
$$\hat{\rho}(h) = \frac{\hat{\gamma}(h)}{\hat{\gamma}(0)}$$