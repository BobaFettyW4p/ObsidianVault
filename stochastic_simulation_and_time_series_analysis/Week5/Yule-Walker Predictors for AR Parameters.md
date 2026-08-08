- to begin with the definition of an AR(p) model:
$$x_t = \Phi_1 x_{t-1} + \Phi_2 x_{t-2} + \dots + \Phi_p x_{t-p} + w_t$$
- want to derive an expressions for the autocovariance for $x_t$
- definition of the autocovariange function:
$$E[x_tx_{t-h}] = \Phi_1 E[x_{t-1} x_{t-h}] + \Phi_2 E[x_{t-2} x_{t-h}] + \dots + \Phi_p E[x_{t-p} x_{t-h}]$$
- a causal, invertible process is stationary
- what about the error function?
$$E[x_{t-h} w_t] = 0 \text{ for } h = 1, 2, \dots, P$$
- the error at any one time is uncorrelated with the error at any previous time
- we only need up to $P$ to derive Yule-Walker
- the trick with this approach is we dont' need to consider the error term
$$\gamma(h) = \Phi_1 \gamma(h-1) + \Phi_2 \gamma(h-2) + \dots + \Phi_P \gamma(h-P) \text{ for } h = 1,2, \dots, P$$
$$\gamma(0) = \Phi_1 \gamma(1) + \Phi_2 \gamma(2) + \dots + \Phi_P \gamma(P) + E[x_t w_t)]$$$$E[x_t w_t] = E[(\sum_{j=0}^{\infty} \psi_i w_{t-j})]$$
- the only time this term is not 0 is when $j=0$
$$ = \psi_0 \bar{w_t}^2 = 1 - \bar{w_t}^2 = \sigma_w^2$$
$$\sigma_w^2 = \gamma(0) - \Phi_1 \gamma(1) - \dots - \Phi_P \gamma(P)$$
## Yule-Walker Equations
$$\gamma(1) = \Phi_1 \gamma(0) + \Phi_2 \gamma(1) + \dots + \Phi_P \gamma(P-1)$$
$$\gamma(2) = \Phi_2 \gamma(1) + \Phi_2 \gamma(2) + \dots + \Phi_P \gamma(P-2)$$
$$\dots$$
$$\gamma(P) = \Phi_1 \gamma(1-P) + \Phi_2 \gamma(2-P) + \dots + \Phi_P \gamma(0)$$
$$\sigma_w^2 = \gamma(0) - \Phi_1 \gamma(1) - \dots - \Phi_P \gamma(P)$$
- forms a linear system with $P$ unknowns
- in matrix form:
$$\hat{\Gamma_P}\hat{\Phi} = \hat{\gamma_P};\ \sigma_w^2 = \hat{\gamma}(0) - \hat{\Phi}^T \hat{\Gamma_P}$$
- with:
![[screenshots/Pasted image 20260718144051.png]]
