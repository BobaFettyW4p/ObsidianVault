
## Why Fourier Analysis for Time Series?
- Goal: Reveal hidden periodic patterns in noisy time series
- Time-domain view can be messy; frequency view can clarify
- Example applications: climate cycles, finance, vibrations, audio
![[Pasted image 20260717204426.png]]

## The Core Question: More Complex Signal
- a realistic time series can have multiple hidden periodic components
- can we detect all underlying frequencies despite the noise?
![[Pasted image 20260717204520.png]]

## The Answer Revealed in Frequency Space
- Fourier analysis reveals four dominant frequencies clearly
- noise is spread across other frequencies and does not obscure the peaks
- complex time-domain signals become simple in the frequency domain
![[Pasted image 20260717204613.png]]

## Two Ways to Describe a Signal
- Time Domain
	- shows the value of the signal at each time point
	- natural for observing trends, sudden events, and raw measure
- Frequency Domain
	- Describes the signal in terms of periodic components
		- frequencies and amplitudes
	- reveals dominant cycles and suppresses random noise
	- useful for filtering, denoising, and spectral analysis
- Fourier analysis lets us translate between these views

## From Signals to Sines and Cosines
![[Pasted image 20260717205023.png]]

- Fourier builds complex signals from simple waves
- Each additional harmonic captures more detail
- Even a jagged or square-like wave is just a sum of smooth oscillations

## Continuous vs. Discrete Fourier Analysis
- Fourier Series (FS)
	- Applies to perfectly periodic signals with period $L$
	- Represents the signal as a sum of discrete frequencies $2 \pi n/L$
	- Useful for signals like seasonalcycles or repeating waveforms
- Fourier Transform (FT)
	- Applies to general, aperiodic but continuous signals
	- Produces a continuous spectrum across all frequencies
	- The basis for most continuous time signal processing
- Discrete Fourier Transform (DFT)
	- applies to sampled, finite signals (realworld time series)
	- produces a finite set of discrete frequencies
	- implicitly assumes the signal repeats every $N$ samples
- Key point: Sampling + finite length -> discrete, finite frequency representation

## Big Picture of the DFT

![[Pasted image 20260717205550.png]]
- Input: $N$ sampled time points $x_0, x_1, \dots x_{N-1}$
- DFT (via FFT) -> $N$ frequency components $X_0, X_1, \dots, X_{N-1}$
- magnitude shows how much each frequency contributes to the signal
- Phase encodes the alignment of each frequency component

## What Success Looks Like
![[Pasted image 20260717205705.png]]
- DFT reveals clear frequency peaks in a messy signal
- Filtering or keeping only dominant frequencies denoises the signal
- This is also the basis of *lossy compression*

## The Main Takeaway: Why Fourier/DFT Matters
- Fourier translates between time and frequency domains
	- time domain: raw signal, trends, and events
	- frequency domain: hidden cycles, dominant frequencies, noise separation
- The Discrete Fourier Transform (DFT):
	- Input: $N$ discrete time samples
	- Output: $N$ discrete frequency components (complex values with magnitude + phase)
	- Lets us detect periodic structure, denoise and compress signals
- Practical notes:
	- Compute efficiently via Fast Fourier Transform (FFT)
	- Real signals have symmetric spectra; only half is unique
	- Maximum resolvable frequency = Nyquist frequency = half the sampling rate
- Big Picture: Fourier transforms messy data into insight, and underpin filtering, spectral analysis, and compression

### Looking Ahead
- Next: Formal definitions and computation of DFT
- Connection to periodograms and spectral density
- How to use Fourier analysis to undersand and filter real data
- ARMA process from the Fourier perspective
