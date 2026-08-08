- assuming $w_t$ is $iid(0, \sigma^2)$, then $E[x_t] = 0$
- the autocovariance of the process is:
$$\gamma(h) = cov(x_t, x_{t+h})$$
$$= E[(\sum_{j=0}^{\infty} \Phi^j w_{t+h-j})(\sum_{k=0}^{\infty} \Phi^k w_{t-k})]$$
$$=E[(w_{t+h} + \dots + \Phi^h w_t + \Phi^{h+1} w_{t-1} + \dots)(w_t + \Phi w_{t-1} + \dots)]$$
Note that:

$$E[w_iw_j] = \sigma_m^2\ if\ i=j$$
- and 0 otherwise
- therefore:
$$\gamma(d) = \Phi^h \sigma_w^2 + \Phi^{h+1} \Phi \sigma^2_w + \Phi^{h+1} \Phi^2 \sigma_w^2 + \dots$$
$$=\sigma_w^2 \sum_{j=0}^{\infty} \Phi^{h+j} \Phi^j$$
$$=\sigma_w^2 \Phi^h \sum_{j=1}^{\infty}\Phi^{2j}$$
$$=\sigma_w^2 \Phi^h \frac{1}{1 - \Phi^2}$$
- likewise, $\rho(h) = \Phi^h$

- Consider an $AR(1)$ process
$$(1 - \Phi_1 B) x_t = w_t$$
$$\Phi(Z) = 1 - \Phi_1 Z$$
- the root is given by:
$$1 - \Phi_1 Z = 0$$
$$z = \frac{1}{\Phi_1} > 1$$
- if $| \Phi_1| < 1$
