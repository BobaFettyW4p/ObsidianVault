
# Introduction
- A *time series* is a set of random variables $x_1, \dots x_n$ observed at times $t_1, \dots, t_n$
	- in general, the processed is described by the joint PDF
$$F(c_z, \dots, c_n) = P(x_1 \leq c_1, \dots, x_n \leq c_n)$$
- in practice, we often work with marginal CDFs and PDFs:
$$F_t(x) = P(x_t \leq x)$$
$$f_t(x) = \frac{\delta F_t(x)}{\delta x}$$
![[screenshots/Pasted image 20260705164322.png]]

#### Mean Function
- The *mean function* of $x_t$ is:
$$\mu_t = E(x_t) = \int_{-\infty}^{\infty} x \cdot f_t(x) dx$$
- Ex: random walk with drift:
$$x_t = \delta t + \sum_{j=1}^t w_j$$

- If $E(w_j) = 0$, then:
$$\mu_t = E(x_t) = \delta t$$
- the expected value may itself change with time

![[screenshots/Pasted image 20260705164722.png]]

#### Correlation
- For random variables $X$ and $Y$:
$$\gamma_{x,y} = E[(X - \mu_x)(Y - \mu_y)]$$
$$ = E[XY] - E[X]E[Y]$$
- Correlation normalizes covariance
$$\rho_{x,y} = \frac{\gamma_{x,y}}{\sqrt{\gamma_{x,x}\gamma_{y,y}}}$$
- correlation measures linear dependence not causality
![[screenshots/Pasted image 20260705164920.png]]

#### Autocovariance
- The *autocovariance* function describes linear dependence between two time points:
$$\gamma_x(s,t) = E[(x_s - \mu_x)(x_t - \mu_t)]$$
$$\gamma_x(t,t) = var(x_t)$$
- The autocorrelation function (ACF) is:
$$\rho(s,t) = \frac{\gamma(s,t)}{\sqrt{\gamma(s,s)\gamma(t,t)}}$$
- $\therefore, -1 \leq \rho(s,t) \leq 1$
![[screenshots/Pasted image 20260705165233.png]]

