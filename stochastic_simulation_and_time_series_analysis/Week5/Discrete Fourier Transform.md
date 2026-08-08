 - a third type of Fourier transform deals with discretely sampled, implicitly period data
	 - such as the time series data we have studied in this class
- The Discrete Fourier Transform (DFT) can be derived from the discretized integral or the Fourier Series Transofrmation
- the ideas are exactly the same
	- we represent the points of the time series exactly as a finite sum of discrete frequencies
$$X_k = \sum_{n=0}^{N-1} x_n e^{-2 \pi i k n/N},\ for\ k = 0,1,2, \dots , N-1$$
## A Few Key Points
- the DFT requires only a finite number of discrete frequencies
- Only a finite number because we are only matching the function at specified points
	- frequencies higher than $\omega = 0.5 \omega_s$ are not resolved in the signal
		- where $\omega_s$ is the sampling rate
		- this is called the *Nyquist frequency*
- Only integer values as the function is implicitly assumed to be periodic of period $L$
- A signal with $N$ real values is transformed into $N$ complex Fourier coefficients in general
	- there is redundant information
		- the first half of the frequencies of the complex conjugate of the second half
- Energy preservation is retained in the discrete sense:
$$\sum_{k=1}^N |X_k|^2 = \sum_{t=1}^N x_t^2$$
- assuming $x_t$ has zero mean
	- otherwise, you need to subtract off the mean
- Convolution theorem applies in a discrete sense
- Always remember: Fourier transform of a real signal is symmetric
	- Fourier transform of a symmetric signal is real
## Computing the Coefficients
- the *scaled periodigram* is defined by Shumway as:
$$P(j/n) = (\frac{2}{n} \sum_{t=1}^n x_t \cos(2 \pi t j / n))^2 + (\frac{2}{n} \sum_{t=1}^n x_t \sin(2 \pi t j/n))^2$$
- this is just the square of the discrete Fourier coefficients at each discrete frequency
	- can be calculated by projections just as with the Fourier Series
- In practice, a fast $O(|n \log n|)$ algorithm is used called the Fast Fourier Transform
	- the result is exactly the same, but it saves $n$ regressions
- For a random process $x_t$ the Fourier coefficients are random, and thus the periodigram is as well
	- we need to take many samples to approximate the true (expected) value
		- this true expected value is the *power spectrum*
- the key point is that the Fourier coefficients ($a_n$ and $b_n$) can be shown to be *pairwise uncorrelated* approximated
	- key result for deriving many important relations

## Periodogram vs. Sample Spectrum
- Shumway defines the quantity $P(\omega j)$ as follows:
	- the "scaled periodogram"
$$P(j/n) = (\frac{2}{n} \sum_{t=1}^n x_t \cos(2 \pi t j/n))^2 + (\frac{2}{n} \sum_{t=1}^n x_t \sin (2 \pi t j/n))^2$$
- the is not exactly the same as the sample power spectrum:
$$I(\omega j)$$
- it is proportional, though
- using Shumway notation:
$$I(\omega j) = d_c^2 (\omega j) + d_s^2 (\omega j)$$
- where $d_c$ and $d_s$ are the cosine and sine transformations, respectively
- It is possible to show that:
$$P(\omega j) = \frac{4}{n} I(\omega j)$$
## Statistical Properties of Coefficients
- extending the ideas of Fourier Analysis to random data is intuitive but requires some subtle technical details to establish validity
- It is important to keep in mind that in the case of random data the Fourier coefficients are *random variables*
- a key results is that the periodogram can be thought of as a sample estimate of the power spectral density
	- it converges to the true spectrum as the number of samples tends to infinity
- another key result is that stationarity implies the Fourier coefficeints are pairwise uncorrelated
	- the converse is true as well

## Convergence of Sample Spectrum to True Spectrum
- using the property of nearly pairwise uncorrelated normal Fourier coefficeints, Shumways shows that confidence intervals can be placed on a simple spectrum as follows:
$$\frac{2 I (\omega_{j:n})}{X_x^2 ( 1 - \alpha/2)} leq f(\omega) \leq \frac{2 I (\omega_{j:n})}{X_2^2 (\alpha/2)}$$
- $X_2^2$ refers to the Chi-squared distribution with two degrees of freedom, and X_2^2(x) denotes evaluating the inverse CDF at point $x$
- $\omega_{j:n}$ refers to the jth frequency estimated by $n$ points
- We will use this experession to place confidence intervals on the soi and recruitment data series

## Smoothing in Spectral Space
- one technique to improve confidence intervals on sample spectra is to do band averaging in spectral space
	- consider a band $\mathcal{B}$ of $L << n$ contiguous frequencies centered around $\omega_j = j/n$
- For frequencies of the form $\omega* = \omega_j + k/n$ let:
$$\mathcal{B} = \{\omega*: \omega_j - \frac{m}{n} \leq w* \leq \omega_j + \frac{m}{n}\}$$
- where $L = 2m +1$
- Now define a smoothed periodogram by averaging:
$$\bar{f}(\omega) = \frac{1}{L} \sum_{k=-m}^m I(\omega_j + k/n)$$
- under suitable assumptions the smoothed spectrum can be shown to have much tighter confidence intervals

## Fourier Transform of the Autocorrelation Function
- the most important result of Shumway regards the autocorrelation function
	- can be derived as follows:
$$\gamma(\tau) = \int_{-\infty}^{\infty} g(t)g(t + \tau) dr$$
$$\mathcal{F}[\gamma(\tau)] = \int_{-\infty}^{\infty} e^{-i \omega \tau} [ \int_{-\infty}^{\infty} g(t) g(t + \tau) dt] d\tau$$
$$ = \int_{-\infty}^{\infty} g(t)[\int_{-\infty}^{\infty}g(\tau +t)e^{-i \omega \tau} d \tau] dt$$
- Note that the Fourier transform of $g(\tau + t)$ is $\hat{g}(\omega)e^{i \omega t}$. Thus:
$$\mathcal{F}[\gamma(\tau)] = \hat{g}(\omega) \int_{-\infty}^{\infty}g(t) e^{i \omega t} dt = |\hat{g}(\omega)|^2$$
- extremely important result
- says the information contained in the autocovariance function is the same as that contained in the power spectrum
	- just give different perspectives
- how to obtain reliable estimates of the power spectrum for a finite signal still need to be addressed

## Power Spectrum of ARMA Processes
- with knowledge of the ACF for ARMA processes, it is straightforward to derive expressions for the power spectral density
- general formula from Shumway:
$$f_x(\omega) = \sigma_w^2 \frac{\Theta(e^{-2 \pi i \omega})|^2}{\Phi(e^{-2 \pi i \omega})|^2}$$
- where $\Phi$ and $\Theta$ are the regular AR and MA operators such that:
$$\Phi(B)x_t = \Theta(B) w_t$$
### Ex. MA(1) Power Spectrum
- Consider the MA(1) process $x_t = w_t + 0.5 w_{t-1}$
	- In this case, we have $\Theta(z) = 1 + 0.5z$
	- Thus:
$$|\Theta(e^{-2 \pi i \omega}|^2) = | 1 + 0.5e^{-2 \pi i \omega}|^2$$
$$=(1 + 0.5e^{-2 \pi i \omega})(1 + 0.5e^{2 \pi i \omega})$$
$$= 1.25 + 0.5(e^{-2 \pi i \omega} + e^{2 \pi i \omega})$$
$$=1.25 + \cos(2 \pi \omega)$$
- This decays from $1.25 \sigma_w^2$ to 0 over the permissible frequencies $[0,0.5]$