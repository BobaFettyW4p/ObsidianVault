# MPCS 58020 Midterm Cheat Sheet
*Covers HW1–HW3: Probability & Simulation, Time Series/ARMA, Regression*

---

## 1. Probability & Sampling

### PDFs / CDFs
- PDF $f(x) \ge 0$, $\int f(x)\,dx = 1$. CDF $F(x) = P(X \le x) = \int_{-\infty}^x f(t)\,dt$.
- $P(a \le X \le b) = F(b) - F(a)$.
- Joint PDF $f(x,y)$: independence $\iff f(x,y) = f_X(x)f_Y(y)$.

### Weak Law of Large Numbers (WLLN)
- $\bar{X}_n = \frac{1}{n}\sum X_i$, $E[\bar X_n] = \mu$, $\mathrm{Var}(\bar X_n) = \sigma^2/n$.
- Chebyshev: $P(|\bar X_n - \mu| > \epsilon) \le \dfrac{\sigma^2}{n\epsilon^2} \to 0$.
- **Intuition:** sample average stabilizes as $n$ grows; individual draws stay random.

### Central Limit Theorem (CLT)
- For iid $X_i$ with mean $\mu$, variance $\sigma^2$:
$$\sqrt{n}\left(\frac{\bar X_n - \mu}{\sigma}\right) \xrightarrow{d} N(0,1)$$
- Practical form: $\bar X_n \approx N(\mu, \sigma^2/n)$ for large $n$, **regardless of the shape of the original distribution**.

### Monte Carlo Estimation & Precision
- Estimate a probability $p$ by simulation: $\hat p = \frac{\text{# successes}}{n}$.
- Standard error of a proportion estimate:
$$SE = \sqrt{\frac{\hat p (1-\hat p)}{n}}$$
- Use $SE$ to decide how many decimal places are meaningful — don't report precision beyond what $SE$ supports.

### Sampling Methods
- **Inverse Transform Method:** if $U \sim \mathrm{Unif}(0,1)$, then $X = F^{-1}(U)$ has CDF $F$.
  - Steps: (1) find CDF $F(x)$, (2) invert to get $F^{-1}(u)$, (3) plug in uniform draws.
- **Rejection Sampling:** to sample from $f(x)$ using an envelope $g(x)$ with $f(x) \le M g(x)$:
  1. Draw $X \sim g$, draw $U \sim \mathrm{Unif}(0,1)$.
  2. Accept $X$ if $U \le \dfrac{f(X)}{M g(X)}$; else reject and repeat.
  - Efficiency depends on how tightly $Mg(x)$ bounds $f(x)$ (smaller $M$ → fewer rejections).

### Correlation vs. Independence (conceptual trap)
- Zero autocorrelation (uncorrelated) **does not imply independence** in general — only under joint normality (or for specific model classes) does zero correlation guarantee independence.

---

## 2. Time Series / ARMA

### Stationarity (weak/covariance stationarity)
A process $x_t$ is stationary if:
1. $E[x_t] = \mu$ (constant, no trend)
2. $\mathrm{Var}(x_t) = \sigma^2$ (constant over time)
3. $\mathrm{Cov}(x_t, x_{t+h})$ depends only on lag $h$, not on $t$

- Stationarity is a property of the **underlying process/parameters**, not of a single observed sample path (a stationary process can still look "off" in a finite sample by chance).
- Visual red flags for non-stationarity: trending mean, changing spread (heteroskedasticity), obvious seasonal/cyclical drift.

### Autocovariance / Autocorrelation
- $\gamma(h) = \mathrm{Cov}(x_t, x_{t+h})$
- $\rho(h) = \gamma(h)/\gamma(0)$, so $\rho(0) = 1$.
- **PACF** $\phi_{hh}$: correlation between $x_t$ and $x_{t+h}$ after removing the linear effect of the observations in between.

### ACF/PACF Identification Table (memorize!)

| Model | ACF | PACF |
|---|---|---|
| **AR(p)** | tails off (decays gradually, possibly oscillating) | **cuts off** after lag $p$ |
| **MA(q)** | **cuts off** after lag $q$ | tails off (decays gradually) |
| **ARMA(p,q)** | tails off | tails off |
| **Random walk** | stays high near 1, decays very slowly | large spike at lag 1, then near 0 |

### AR(1): $x_t = \phi x_{t-1} + w_t$
- Stationary/causal iff $|\phi| < 1$.
- $\gamma(0) = \sigma_w^2/(1-\phi^2)$, $\rho(h) = \phi^{|h|}$.
- $\hat\phi \sim AN(\phi,\ n^{-1}(1-\phi^2))$ (large-sample variance of the estimator).

### AR(2): $x_t = \phi_1 x_{t-1} + \phi_2 x_{t-2} + w_t$
**Fast stationarity check — triangle conditions (use these under time pressure instead of root-finding):**
$$\phi_1 + \phi_2 < 1, \qquad \phi_2 - \phi_1 < 1, \qquad |\phi_2| < 1$$
All three must hold.

**General AR(p) stationarity (root condition):** write the characteristic polynomial
$$\phi(z) = 1 - \phi_1 z - \phi_2 z^2 - \cdots - \phi_p z^p$$
Process is **stationary/causal** iff all roots $z$ of $\phi(z)=0$ lie **outside the unit circle** ($|z|>1$).

### Invertibility (MA models) — a separate concept from stationarity
- For $x_t = w_t + \theta_1 w_{t-1} + \cdots + \theta_q w_{t-q}$, define $\theta(z) = 1+\theta_1 z + \cdots+\theta_q z^q$.
- **Invertible** iff all roots of $\theta(z)=0$ lie outside the unit circle (lets you write $w_t$ as a convergent function of past $x_t$'s).
- MA models are *always* stationary (finite sum of white noise) — invertibility is about whether the process is uniquely identifiable/representable as an AR($\infty$), not about stationarity.

### Forecasting (recursive, minimum MSE forecast = conditional expectation)
For AR(2): $x_t = \phi_1 x_{t-1} + \phi_2 x_{t-2} + w_t$, given last observed values $x_n, x_{n-1}$:
$$\hat x_{n+1} = \phi_1 x_n + \phi_2 x_{n-1}$$
$$\hat x_{n+2} = \phi_1 \hat x_{n+1} + \phi_2 x_n \quad \text{(plug in the forecast, not a new noise term)}$$
- Future $w_t$'s are set to their expectation (0).
- **Forecast uncertainty increases with horizon** — each step forward compounds accumulated noise variance.

### Forecast Error Variance (AR(1), by hand)
For AR(1) $x_t = \phi x_{t-1}+w_t$, the $h$-step-ahead forecast error variance is:
$$\mathrm{Var}(x_{n+h}-\hat x_{n+h}) = \sigma_w^2\sum_{j=0}^{h-1}\phi^{2j}$$
- $\sum_{j=0}^{h-1}$ means $j$ runs from $0$ to $h-1$ — i.e. **$h$ terms total**. E.g. $h=2$ → terms at $j=0,1$; $h=3$ → terms at $j=0,1,2$.
- Each term $\phi^{2j}$ represents the (squared) surviving contribution of one not-yet-observed future noise term $w$; each additional forecast step adds exactly one new term to the sum.
- **As $h\to\infty$:** this is a geometric series with ratio $\phi^2<1$ (since $|\phi|<1$ for stationarity), so it converges:
$$\sum_{j=0}^{\infty}\phi^{2j} = \frac{1}{1-\phi^2} \implies \mathrm{Var}(x_{n+h}-\hat x_{n+h}) \to \frac{\sigma_w^2}{1-\phi^2} = \gamma(0)$$
- **Why it levels off instead of growing forever:** the forecast $\hat x_{n+h}\to \mu$ (the unconditional mean) as $h$ grows, so eventually you're just guessing "the long-run average" — and the error variance of that guess converges to the process's own unconditional variance $\gamma(0)$, since at that point you have no more information than an unconditional draw from the process.

### Estimation methods (AR models)
- Yule-Walker (method of moments), OLS, MLE — all give consistent, asymptotically optimal estimators under standard conditions.
- Diagnostics: residual ACF should look like white noise; Ljung-Box/Portmanteau test for residual autocorrelation; check homoscedasticity of residuals.

---

## 3. Regression

### Unbiasedness (general estimator property)
An estimator $\hat\theta$ is **unbiased** for a parameter $\theta$ if
$$E[\hat\theta] = \theta \quad \text{exactly (for every sample size } n\text{)}$$
- This is a statement about the *center* of the sampling distribution — no systematic over- or under-estimation on average across repeated samples.
- Quick check pattern: use linearity of expectation. E.g. for $\hat\mu=\frac{1}{n}\sum X_i$ with $E[X_i]=\mu$: $E[\hat\mu] = \frac{1}{n}\sum E[X_i] = \frac{1}{n}(n\mu)=\mu$ → unbiased.
- Adding/subtracting any nonzero constant to an unbiased estimator makes it **biased** by exactly that constant: if $\hat\theta$ is unbiased for $\theta$, then $E[\hat\theta + c] = \theta + c \ne \theta$ (for $c\ne0$).
- **Unbiasedness alone doesn't make an estimator good** — variance matters too (this is exactly why Gauss-Markov specifies BLUE: *best* refers to lowest variance *among the unbiased* class, not unbiasedness by itself).

### OLS Setup
- Model: $y = X\beta + \varepsilon$, error $= y - \hat y$.
- OLS minimizes $\sum (y_i - \hat y_i)^2$ (sum of squared errors).

### Normal Equations (how to actually compute $\hat\beta$ by hand)
Setting the derivative of the squared-error objective to zero gives the **normal equations**:
$$X^TX\,\hat\beta = X^Ty$$
As long as $X^TX$ is invertible, solve by left-multiplying both sides by $(X^TX)^{-1}$:
$$\hat\beta = (X^TX)^{-1}X^Ty$$
- On an exam, $(X^TX)^{-1}$ will typically be **given** — you are not expected to invert a matrix by hand, only to (1) write down the normal equations from the given $X^TX$ and $X^Ty$, and (2) carry out the matrix–vector multiplication.
- **2×2 matrix–vector multiply**, the pattern you'll actually need:
$$\begin{pmatrix}a&b\\c&d\end{pmatrix}\begin{pmatrix}p\\q\end{pmatrix} = \begin{pmatrix}ap+bq\\cp+dq\end{pmatrix}$$
- For fitting an **AR(2) by regression**: predictors are $x_{t-1}, x_{t-2}$, response is $x_t$, and the two entries of $\hat\beta$ are the fitted $\hat\phi_1, \hat\phi_2$ — i.e. the fitted model is $\hat x_t = \hat\phi_1 x_{t-1} + \hat\phi_2 x_{t-2}$.
- Equivalent alternative: write out $X^TX\hat\beta = X^Ty$ as a 2-equation linear system and solve directly by substitution/elimination (same answer as using the given inverse — just more arithmetic).

### Gauss–Markov Theorem
**Assumptions needed:** linear model, errors have mean 0, constant variance (homoskedasticity), uncorrelated errors (no assumption of normality needed).

**Conclusion:** Under these assumptions, OLS is **BLUE** — Best (minimum variance) Linear Unbiased Estimator.
- "Best" = lowest variance **among all linear unbiased estimators** (not all estimators in general — a biased estimator like ridge regression can have lower MSE).
- Lower variance ⇒ across repeated samples, $\hat\beta$ fluctuates less around the true $\beta$ ⇒ more stable/reliable, especially in noisy data.
- **This is a proven theorem, not an empirical claim** — it holds whenever the assumptions hold.
- **Breaks down** (Gauss-Markov no longer guarantees BLUE) under: heteroskedasticity, autocorrelated errors, nonlinearity in parameters, or if you allow biased estimators into consideration.

### Estimator variance vs. in-sample fit (common confusion)
- **Variance of an estimator** = how much $\hat\beta$ would change across *different hypothetical samples* — a property of the sampling distribution.
- This is **not** the same as residual size / in-sample fit (e.g., $R^2$ or SSE), which describes how well the model fits *this particular* dataset.

---

## Quick Reference: Common Pitfalls
- Stationarity describes the process, not any one realization.
- "Uncorrelated" ≠ "independent" unless jointly normal.
- Estimator variance = sampling variability, not in-sample residual size.
- Invertibility ≠ opposite of stationarity — separate, orthogonal concept (invertibility is about MA representability, stationarity about AR causality).
- AR(2) fast check: use the triangle inequalities, not root-finding, when under time pressure.
- Forecast recursion plugs in *forecasts* for unknown future $x$'s, and 0 for future noise terms.