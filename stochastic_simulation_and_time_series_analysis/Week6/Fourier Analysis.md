## Frequency-Domain Analysis of Time Series

- given a signal $x(t)$, what periodic behavior is hidden inside it?
- many phenomena have inherent periodicities
	- mixture of peridicities
		- hard to perfectly extract them due to noise, imperfect sampling, errors
			- point of Fourier analysis is to reveal the periodic nature of the data in the way physical space inspection cannot do
				- you are unveiling something hidden your eye cannot see

## Time Domain vs. Frequency domain
- time domain
	- shows when events occur
- frequency domain
	- shows which frequencies are present
- two different representations of the same signal

### Key Properties

- any sufficiently well-behaved signal can be represented as a sum of sinusoids
- large Fourier coefficients indicate dominant periodicities
- low frequencies correspond to slow variation
- high frequencies correspond to rapid variation
- real-value signals produce symmetric spectra
- when you have a discrete signal, you can always use a FFT to find the $n$ Fourier coefficients

### The Mathematics
- given $N$ samples,
$$x_0, x_1, \dots, x_{N-1}$$
- the Discrete Fourier Transform computes:
$$X_k = \sum_{n=0}^{N-1} x_n e^{-2 \pi i k n/N}, k = 0, \dots, N-1$$
- can use the FFT function to return the Fourier values
- Observation: The Fourier transform correctly identified 
- won't be asked to reproduce on the exam
- won't be going too deep, just need to understand how it fits it, how to think about it, key conclusions