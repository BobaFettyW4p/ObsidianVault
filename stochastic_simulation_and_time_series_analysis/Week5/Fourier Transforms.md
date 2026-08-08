- Fourier series are for representing $L$-periodic functions
- Their distinguishing feature is that only discrete frequencies are required; specifically, $2 \pi n /L$
	- in this case, the period $L = 2 \pi$ only integer frequencies are required
- The Fourier Transform extends this idea to general, non-periodic square integrable functions
- Deriving this involves taking the limit ast he period goes to infinity

## Definition

- The Fourier Transform and Inverse Fourier Transform Pair are defined as follows:
$$\hat{f}(\omega) = \frac{1}{2 \pi} \int_{-\infty}^{\infty} f(t) e^{-i \omega t} dt$$
$$f(t) = \int_{-\infty}^{\infty} \hat{f}(\omega) e^{i \omega t} d\omega$$
- $\hat{f}(\omega)$ is called the Fourier Transform of $f(t)$
	- Under suitable conditions it is invertible
		- $f(t)$ is considered the *Inverse Fourier Transform* of $\hat{\omega}$
	- different sources define the normalization differently

- Example Fourier Transform pairs:


| f(t)                                     | $\hat{f}(\omega)$                                         |
| ---------------------------------------- | --------------------------------------------------------- |
| 1                                        | $\delta(\omega)$                                          |
| a                                        | $a \delta(\omega)$                                        |
| 1 for t $\in [\frac{a}{2}, \frac{a}{2}]$ | $\sin c(a \pi \omega)$                                    |
| $e^{-\pi t^2}$                           | $e^{- \pi \omega^2}$                                      |
| $\cos(2 \pi s t)$                        | $\frac{1}{2} [ \delta(\omega + s) + \delta(\omega - s)]$  |
| $\sin(2 \pi s t)$                        | $\frac{1}{2} i [\delta(\omega + s) + \delta(\omega -s )]$ |

### Properties of Fourier Transforms
- Linearity
$$\mathcal{F}(f+g) = \mathcal{F}(f) + \mathcal{F}(g)$$
$$\mathcal{F}(af) = a \mathcal{F}(f)$$
- Energy Preserving
$$\int_{-\infty}^{\infty} | f(t)|^2 dt = \int_{-\infty}^{\infty} | \hat{f}(\omega)|^2 d \omega$$
- where $|\hat{f}(\omega)|^2$ is called the *power spectrum*
- Power spectrum is invariant to shifts:
$$|\mathcal{F}(f(t))|^2 = |\mathcal{F}(f(t-a))|^2$$

## Convolution Theorem
- one of the most important properties of Fourier transforms is the *Convolution Theorem*
- Denote the convolution between two functions $f(t)$ and $g(t)$ as:

$$f \times g = \tilde{f}(t) = \int_{-\infty}^{\infty} f(\tau) g(t - \tau) d \tau$$
- it can be shown using the definition of the Fourier Tranform that:
$$\mathcal{F}(f * g) = \mathcal{F} (f) \mathcal{F}(g) = \hat{f}\hat{g}$$
- e.g. the convolution of two functions is just the product of their Fourier Transforms
	- Thus we have:
$$f * g = \mathcal{F}^{-1} (\hat{f} \hat{g})$$

## Convolution Theorem and Signal Filtering
- The convolution theorem is an important property with profound impacts for signal processing
- Convolution is a common operation in signal processing that amounts to em filtering 
- To see this, consider our standard weighted running average
	- define an $N$ point stationary random signal as:
$$\underline{x} = [x_1, x_2, x_3, \dots, x_N$$
- and a filter of $P$ non-zero weights:
$$\underline{g} = [g_1, g_2, g_3, \dots, g_P]$$
### Discrete Convolution

- for a running average, e.g., the $\underline{g}$ woudl be

$$\underline{g} = [\frac{1}{P}, \frac{1}{P}, \dots]$$
- Consider a process that filters $f$ using $g$, using running average as a conceptual model. We would have:
$$\tilde{x_1} = x_1 g_1 + x_2 g_2 + \dots + x_p g_p$$
$$\tilde{x_2} = x_2 g_1 + x_3 g_2 + \dots + x_{p+1} g_p$$
$$\dots$$
$$\tilde{x_{N-P+1}} = X_{N-P +1} g_1 + x_{N-P+1}g_2 + \dots + X_N g_P$$
- Using summation notation we have:
$$\tilde{f_i} = \sum_{k=1}^P x_{k+i-1} g_k,\ 1 \leq i \leq n-p+1$$
- while they look plausibly similar, it can be shown that a discrete version of the convolution integral exactly corresponds to our version above
- Note we can write the convolution as matrix operation
	- What are the conditions of invertibility?

### Filtering
- thinking of convolution as an arbitrary weight running average (or difference) operator connects convolution to filtering
	- removing and accentuating certain frequencies in a signal
- The convolution theorem tells us that filtering in the convolution sense is the equivalent of weighting frequency in Fourier space
	- It is often much more intuitive to think of filtering directly in Fourier space
		- consider the shape of $\hat{g}(\omega)$ and consider how it weights the Fourier frequencies