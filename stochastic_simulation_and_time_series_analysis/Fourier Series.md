## Basics
- Fourier Series apply to functions that are periodic over some period $L$
- Any square integrable, $L$-periodic function can be expanded into an infinite sum of pure waves of amplitude $A_n$, discrete frequencies $2 \pi n / L$, $n = 1,2, \dots$ with phase $\Phi_n$
$$f(x) = \frac{A_0}{2} + \sum_{n=1}^N A_n \sin(\frac{2 \pi n x}{L} + \Phi_n)$$
- The Fourier Series is guaranteed to converge to $f(x)$ at almost every point, but for engineering applications periodicity and square integrability are typically considered sufficient conditions for convergence

## Other Ways of Writing the Series
- Notice that the Fourier expansion can equivalently be written as:

$$f(x) = \frac{a_0}{2} + \sum_{n=1}^N (a_n \cos(\frac{2 \pi n x}{L}) + b_n \sin(\frac{2 \pi n x}{L}))$$
- where the coefficients are given as:
$$a_n = A_n \sin(\Phi_n)$$
and:
$$b_n = A_n \cos(\Phi_n)$$

### Complex Notation
- an even more convenient form is to use complex polynomials:
$$f(x) = \sum_{n = -N}^N c_n e^{\frac{i 2 \pi n x}{L}}$$

- where $c_n$ equals:
	- $\frac{1}{2} (a_n - i b_n)$ for $n > 0$
	- $\frac{a_0}{2}$ for $n=0$
	- $c*_{|n|}$  for $n < 0$
		- Notice that the summation using this convention runs from $-N$ to $N$ and that half of the coefficients are redundnat (complex conjugates) assuming that $f$ is real-valued

## Solving For Coefficients
- how do we solve for the coefficients $c_n$ ?
- It is straightforward given orthogonality of each term in the series
	- note that:
$$\int_{\frac{-L}{2}}^{\frac{L}{2}} e^{2 \pi i n x/L} e^{-2 \pi i m x / L} = 0$$
	- for $m \neq n$
- Thus, we can compute the coefficients one by one as:
$$c_n = \frac{1}{L} \int_{\frac{-L}{2}}^{\frac{L}{2}} f(x) e^{-2 \pi i n x/L} dx$$
- the equivalent expressions in terms of sin/cos expansion is:
$$a_n = \frac{2}{L} \int_{\frac{-L}{2}}^{\frac{L}{2}} f(x) \cos (\frac{2 \pi n  x}{L}) dx$$
$$b_n = \frac{2}{L} \int_{\frac{-L}{2}}^{\frac{L}{2}} f(x) \sin(\frac{2 \pi n x}{L}) dx$$
- thus, we should think of the complex form as having a real part, which is the cosine terms, and an imaginary part which is the sine part

## Examples
- consider the following function:
$$f(x) = \frac{x}{\pi}$$
- for $- \pi < x< \pi$
$$f(x + 2 \pi k) = f(x)$$
- for $-\infty < x < \infty$ and $k \in Z$
- Evaluate the integrals:
$$a_n = \frac{1}{\pi} \int_{-\pi}^{\pi} f(x) \cos(nx)dx = 0$$
$$b_n = \frac{1}{\pi} \int_{-\pi}^{\pi} f(x) \sin(nx) dx = \frac{2(-1)^{n+1}}{\pi n}$$

- Thus, the function $f(x)$ can be expanded as:
$$f(x) = \frac{2}{\pi} \sum_{n=1}^{\infty} \frac{(-1)^{n+1}}{n} \sin(nx)$$
- This series, since $f(x)$ is discontinuous at $(2n+1)\pi$, converges to $f(x)$ everywhere except at odd multiples of $\pi$, where it converges to $0$