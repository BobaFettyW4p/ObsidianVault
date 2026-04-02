# Cache Basics: Large and Fast: Exploiting Memory Hierarchy
### Quiz Review
a rough order of magnitude of comparison:
- register compare: takes one clock cycle
- register add: one clock cycle
- load from cache: a few cycles (1-10 depending on cache level)
- load from RAM: typically on the order of 100s of cycles
> "If you learn nothing, learn the memory gap"

- Moore's Law explicitly predicts the rate of increase in transistor density
	- a lot of people associate with clock speed scaling
		- this is an indirect relationship
- v-threshold, need to be able to differentiate between a 0 and 1 state
	- a lot of leakage
	- unpredictable behavior
- multicore
	- more favorable power scaling
	- more, simpler cores keeps the scaling under control while delivering more performance
> "When will we build the first fault tolerant 100 qubit supercomputer?"
## go back and review SISD, MIMD, MISD, SIMD
- Multiple Instruction, Multiple Data

- *spatial locality* refers to the idea that data located close to each other is often accessed together
	- accessing sequential elements in an array
- programmers want unlimited amounts of memory with low latency
- fast memory technology is more expensive per bit than slower memory technology
- **Solution**: organize memory system into a hierarchy
	- entire addressable memory space available in the largest, slowest memory
	- incrementally smaller and faster memories, each containing a subset of the memory below it, proceed in steps up toward the processor
- what constitutes 'low latency'?
	- you can get data at about the same rate that you can compute it
- Memory Performance Gap
- Temporal and spatial locality ensure that nearly all references can be foundin smaller memories
	- gives the illusion of a large, fast memory being presented to the processor
### Principles of Locality
- programs access a small proportion of their address space

### Memory Hierarchy Levels
- Block aka line
	- unit of copying
		- may be multiple words
- if accessed data is present in the upper level
	- hit: access satisfied y upper level
		- hit ratio: hits/accesses
	- if accessed dataa is absent
		- miss: block copied from lower level
			- time taken: miss penalty
			- miss ratio: misses/accesses = 1 - hit raio
			- then accessed data is supplied rom an upper level
	- 