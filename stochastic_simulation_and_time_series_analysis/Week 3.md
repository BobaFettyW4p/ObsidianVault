- syllabus is posted at the beginning of main
	- topics, main area each week
# Time Series  Analysis - Basics

- models
	- first you make judgements qualitatively
	- then you make numerical fin
> periodic structure?

> what does it mean to be periodic, and what does it mean to have a well-defined mean?

> auto-correlation function

- neighboring points are more likely to be close to each other from where they are than the mean

#### Introduction
- a *time series* is a set of random variables $x_1, \dots, x_n$ observed at times $t_1, \dots, t_n$
	- in general, the process is described by the joint CDF
$$ f(c_z, \dots, c_n) = P\{x_a \leq c_1, \dots , x_n \leq c_n \}$$
- in practice, we often work with marginal CDFs and PDFs
$$F_t(x) = P\{x_t \leq x\}$$
$$f_t(x) = \frac{\delta F_t(x)}{\delta x}$$

#### Mean Function
- The *mean function* of $x_t$ is:
$$\mu_t = E(x_t) = \int_{-\infty}^{\infty} xf_t(x)dx$$
- e.g. random walk with drif:
$$x_t = \delta t + \sum_{j=1}^t w_j$$
- if $E(w_j) = 0$, then:
$$\mu_t = E(x_t) = \delta t$$
- the expected value may itself change with time


#### Mean Function Examples
- note that the expected value is a linear operator:
$$E[X+Y] = E[X] + E[Y]$$
$$E[aX] = aE[X]$$
- the expectation only acts on the random part of the model
- Ex. Given white noise $w_t$ smoothed with a boxcar filter of width 3,
$$\mu_{v_t} = \frac{1}{3}[E(w_t -1) + E[w_t] + E(w_{t+1})] = 0$$
- Ex.
$$\mu_t = E[2 \cos(\frac{2 \pi t}{50} + .6\pi) + w_t]$$
$$= 2 \cos (\frac{2 \pi t}{50} + .6 \pi)$$
- by linearity of expectation, the expected value of two sums is the sum of their expected value

#### Correlation
- for random variables $X$ and $Y$:

$$\gamma_{x,y} = E[(X - \mu_x)(Y-\mu_y)]$$
$$=E[XY] - E[X]E[Y]$$
- Correlation normalizes covariance:
$$ \rho_{x,y} = \frac{\gamma_{x,y}}{\sqrt{\gamma_{x,x}\gamma_{y,y}}}$$
- correlation measures linear dependence, not causality

#### Autocovariance
- the *autocovariance* function describes linear dependence between two tiem points:

$$\gamma_x(s,t) = E[(x_s - \mu_s)(x_t - \mu_t)]$$
$$\gamma_x(t,t) = var(x_t)$$
- the *autocorrelation function (ACF)* is:
$$\rho(s,r) = \frac{\gamma(s,t)}{\sqrt{\gamma(s,s)\gamma(t,t)}}$$
- Thus, $-1 \leq \rho(s,t) \leq 1$

# Ensembles
#### The estimation Problem
- we have defined quantities such as:
	- the mean function
	- covariance
	- autocovariance
- how do we estimate these quantities from observed data?
- the challenge is that we usually observe each random variable only once
- where do repeated samples come from?
#### Ensemble Averages
- imagine repeating the same experiment many times under identical conditions
- each repetition produces another realization of the same process
- if we have $m$ realizations, the ensemble estimate of the mean at time $t$ is

#### Why Ensembles are Rare
- ensemble averages are the ideal way to estimate statistical quantities
- unfortunately, we usually observe only one realization of a processs
	- one century of temperatures
	- one history of the stock market
	- one patient's ECG
	- one earthquake recording
- how can one 

# Stationarity
- in practice, we usually only have one realization fo a time series
- to estimate statistical quantities from a single realization, we need an additional assumption
	- the key assumption is *stationarity*
- a time series is *strictly stationary* if its probabilistic behavior is unchanged by a shift in time
- the join distribution of $\{x_1, \dots, x_n\}$ is identic

- a weaker and more practical assumption is *weak stationarity*
	- a weakly stationary process satisfies:
		- themean is constant:
$$\mu_t = \mu$$
		- the autocovariance depends only on the lag:
$$\gamma(s,t) = \gamma(s+h, t+h)$$
		- equivalently:
$$\gamma(s,t) = \gamma(s-t)$$
- for a stationary time series, the same mean becomes:
$$\bar{x} = \frac{1}{n} \sum_{i=1}^n x_i$$
- if the samples are uncorrelated:
$$var(\bar{x}) = \frac{\sigma^2}{n}$$

#### Estimation of Correlation
- when analyzing real data