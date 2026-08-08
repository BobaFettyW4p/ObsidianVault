- new model for time series
	- fundamentally different
		- future values of the time series have a dependence on previous values
			- before, they're at the same time
				- can do it by lagging a time series
>key idea: future values depend on some values that came before

- this is a theoretical model for a time series
	- only useful if we can look at real data and infer that this structure might describe what we're seeing and can use our data to make predictions into the future
- given a time series $x_t$
$$x_t = \Phi_1 x_{t-1} + \Phi_2 x_{t-2} + \dots + \Phi_p x_{t-p} + w_t$$
- $x_t$ is the time series
- $\Phi_1, \Phi_2, \dots, \Phi_p$ are our *autoregressive parameters*
	- we'll look at what values they can take on, what these values mean
- $w_t$
	- our "errors"
		- the part not explained by the model
			- we assume they are iid
- "order P AutoRegressive processes: $AR(p)$
$$x_t - \Phi_1 x_{t-1} - \Phi_2 x_{t-2} - \dots - \Phi_p x_{t-p} = w_t$$
- to solve, we define a simple operatior $B$ such that:
$$B x_t = x_{t-p}$$
- called the *lag* or *backshift operator*
- it follows that:
$$B^p x_t = x_{t-p}$$
- for any value of $p$
- if we define this linear operator, we can rewrite the AR process as follows:
$$x_t = \Phi_1 B x_t - \Phi_2 B^2 x_t - \dots - \Phi_1 B^p x_t = w_t$$
- if we factor, we get a nice elegant form:
$$(1 - \Phi1 B - \Phi_2 B^2 - \dots) x_t = w_t$$
- we call this expression $\Phi(B)$
	- this is an important entity in many analysis
	- called the *autoregressive polynomial*
$$\Phi(B) = w_t$$
- let us turn to an $AR(1)$ process:
$$(1 - \Phi_1 B)x_t = w_t$$
- what does this even say?
- in its original form:
$x_t = \Phi_1 x_{t-1} + w_t$$
- what does this say?
	- at any point $t$, the value of $x$ is some random unmodeled piece of the phenomena we're observing
		- IID values from some distribution
	- together with some piece of the previous value ($\Phi_1$), we can gain intuition to the value of $x_t$
		- has a bit of memory