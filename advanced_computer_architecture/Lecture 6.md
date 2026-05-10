### STREAM
- what does "memory bandwidth" mean?
	- toll booth analogy
	- the term is often used in two different ways:
		- hardware peak: the DRAM interface limit
		- sustained bandwidth: the rate a program actually achieves
- STREAM measures sustained bandwidth
	- for large arrays
	- with regular, unit-stride access
	- under favorable streaming conditions
- it does not measure general memory performance
- stream and membench come together to create the roof line performance
### What STREAM does
- simple vector kernels over large arrays
- what each kernel does:
	- copy: `A[i] = B[i]`
	- scale: `A[i] = s * B[i]`
	- add: `A[i] = B[i] + C[i]`
	- triad: `A[i] = B[i] + s * C[i]`
- long loops, very little computation
	- low arithmatic intensity (1/24)

