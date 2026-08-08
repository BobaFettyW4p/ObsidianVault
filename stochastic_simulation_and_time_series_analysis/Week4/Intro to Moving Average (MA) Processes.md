- $MA(q)$ is defined as:
$$x_t = w_t + \Theta_1 w_{t-1} + \Theta_2 w_{t-2} + \dots + \Theta_q w_{t-q}$$
- define an MA operator:
$$\Theta (B) = (1 + \Theta B + \Theta_2 B^2 + \dots + \Theta_z B^q)$$
- when we write the MA operator this way, we can write the MA process as follows:
$$x_t = \Theta(B) w_t$$
- can we write the inverse MA operator solely in terms of $x_t$?
- the first question:
	- what is the expected value of an MA process?
$$E(x_t) = 0$$
- this is independent of the choice of $\Theta$
- $\gamma (h)$ for MA(1) process:
$$\gamma(h) = (1 + \Theta)^2 \sigma_w^2 $$
- if $h = 0$
$$\gamma(h) = \Theta \sigma_w^2$$
- if $h = 1$
$$\gamma(h) = 0$$
- if $h > 1$

$$\rho(h) = \frac{\Theta}{1 + \Theta^2}$$
- if $h=1$
- $0$ if $h>1$
