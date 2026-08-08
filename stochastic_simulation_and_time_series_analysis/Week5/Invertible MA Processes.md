## MA(q) processes
- a moving average model of order $q$ is called $MA(q)$ and is defined as:
$$ x_t = w_t + \Theta_1 w_{t-1} + \Theta_2 w_{t-2} + \dots + \Theta_q w_{t-q}$$
- In the AR model, $x_t$ was a combination of the previous $x$ terms, but in the MA model, $x_t$ is a combination of the past $w$ terms
- We define the moving average operator $\Theta(B)$ as:
$$\Theta(B) = (1 + \Theta B + \Theta_2 B^2 + \dots + \Theta_q B^q)$$
- the equation will finally become:
$$x_t = \Theta(B) w_t$$

### MA(1) Processes
- For an MA(1) process, it can be shown that:
$$x_t = w_t + \Theta_1 w_{t-1}$$
$$E(x_t) = 0$$
$$
 \gamma(h) = \begin{cases} (1+\theta^2)\sigma_w^2 & \text{if } h = 0 \\ \theta \sigma_w^2 & \text{if } h = 1 \\ 0 & \text{if } h > 1 \end{cases}
$$
$$
\rho(h) =  \begin{cases}  \frac{\Theta}{1 + \Theta^2} & \text{if } h = 1 \\ 0 & \text{if } h > 1 \end{cases}
$$
- As one might expect, the $\rho(h)$ goes to zero for $h > 1$
-  In general for an $MA(q)$ process $\rho(h) = 0$ for $h > q$

## MA Processes - Parameter Restrictions
- unlike AR process, MA processes are stationary independent of the choice of $\Theta_i$
- There is one complication involving parameter selection
- Note that $\rho(h) = \frac{\Theta}{1+\Theta^2}$ is the same for both $\Theta$ and $\frac{1}{\Theta}$
- Thus, consider two MA(1) processes with $\Theta_1 = 0.5$ and $\sigma_w^2 = 1$:
$$x_t = w_t + \frac{1}{5} w_{t-1} \text{ with } w_t = i.i.d(0, 25)$$
$$y_t = v_t + 5 v_{t-1} \text{ with } v_t = i.i.d(0,1)$$
- these are indistinguishable statistically since the noise term cannot be observed independently
- by convention, we always choose the representation that is *invertible*, e.g can be written as an infinite AR process
- For example, for an MA(1) process we can use substitution to write:
$$w_t = -\Theta w_{t-1} + x_t$$
$$w_t = \sum_{j=0}^{\infty} (-\Theta)^j x_{t-j}$$
- This converges only for $| \Theta < 1|$, so we choose $x_t$ over $y_t$ by convention
- This idea can be extended to MA(q) models
	- i.e. the root of the MA(q) polynomial must lie outside the unit circle
