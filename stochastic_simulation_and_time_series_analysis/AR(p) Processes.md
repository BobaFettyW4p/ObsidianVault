## Time Series: AR Models

- we will study several models for describing time series with lagged autocorrelation:
	- *AR*: autoregressive model
	- *MA*: moving average model
	- *ARMA*: autoregressive moving average model
	- *ARIMA*: autoregressive integrated moving average model
- classical regression describes a static process;
	- that is, current values are a function of other values at the *same* timestep, i.e.
$$x_t = f(z_{1t}, z_{2t}, \dots)$$
- AR is one model that allows *past* values to influence the current values
- An AR model of order $P$ is called an $AR(P)$ process and is described as:
$$x_t = \Phi_1 x_{t-1} + \Phi_2 x_{t-2} + \dots + \Phi_p x_{t-p} + w_t$$
- we can simplify this expression by defining the *backshift* operator $B$ such that:
$$B^k x_t = x_{t-k}$$
- the backshift operator allows us to express this as:
$$x_t (1 - \Phi_1 B - \Phi_2 B^2 - \dots - \Phi_p B^p) = w_t$$
- furthermore, we can define the AR operator $\Phi$ as:
$$\Phi(B) = 1 - \Phi_1 B - \Phi_2 B - \dots - \Phi_p B^p$$
- finally, the backshift operator lets us express this as:
$$\Phi(B)x_t = w_t$$
## Inverting an AR(1) Process
- The AR(1) process is:
$$x_t = \Phi x_{t-1} + w_t$$
$$= \Phi^2 x_{t-2} + \Phi w_{t-1} + w_t$$
$$\dots$$
$$= \Phi^k x_{t-k} + \sum_{j=1}^{k-1} \Phi^j w_{t-j}$$
- If $| \Phi | < 1$, then $\Phi^k \rightarrow 0$ as $k \rightarrow \infty$ so:
$$x_t = \sum_{j=1}^{\infty} \Phi^j w_{t-j}$$
## Statistics of an AR(1) Process
- Assuming $w_t$ is $iid(0, \sigma^2)$, then $E[x_t] = 0$
- The autocovariance of the procss is:

$$\gamma(h) = cov(x_t, x_{t+h})$$
$$= E[(\sum_{j=0}^{\infty} \Phi^j w_{t+h - j})(\sum_{k=0}^{\infty} \Phi^k w_{t-k})]$$
$$=E[(w_{t+j} + \dots + \Phi^h w_t + \Phi^{h+1} w_{t-1} + \dots)(w_t + \phi w_{t-1} + \dots)]$$
- Note that:
$$E[w_i w_j] = \sigma^2_m$$
- if $i = j$, and 0 otherwise
- Therefore
$$\gamma(h) = \Phi^h \sigma_w^2 + \Phi^{h+1}\Phi \sigma_w^2 + \Phi^{h+1} \Phi^2 \sigm