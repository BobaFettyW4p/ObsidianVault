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
### Using midway3 and PAPI
- PAPI - pap-ee - way to get access to the hardware counters, i.e. registers that record how our algorithme xercises the hardware and give us insight into how it performs and why
	- give us insights to improve
- `ssh midway.rcc.uchicago.edu -l ivancicm`
	- brings you to a login node
		- lets you compile, access shared file system
			- not intended for serious computing
			- not intended for performance analysis
				- it's a shared resource
- need to request a compute node from the login node
	- two ways to access them:
		- share with other peopel - we don't want this
		- access them exclusively
			- whatever we run is the only thin running on the node
				- gives us the most reliable data
- `rcchelp sinfo`
	- gives a list of partitions
	- midway is built as login nodes with compute nodes
		- compute nodes are separated into partitions, which denotes the type of hardware
	- `caslake` is the largest and is the standard hardware to request
- `sinteractive --account=mpcs52018 --partition=caslake --time==00:20:00`
	- requests an interactive session
	- specifies the account, partition, and time requested for
- `lscpu`
	- gives you data on the node you land on
- `module load papi`
	- can load other things like `emacs` or `intel`
	- `module avail`
	- use `papi/6.0` in this class
- PAPI gets embedded in your code, but it also comes with a set of binaries that are useful for a particular installation of it
- `papi_avail`
	- lists all events that occur in the hardware that are defined in PAPI and can be counted
- to actually compile:
	- `gcc papi_test.c -o papi_test -lpapi`
- best to only measure one thing at a time to start

### Quantitative Analysis of Cache Performance
- come up with a simple model and measured impact performance
	- determine the memory hierarchy for given setups
- take a complex system, model its essential features, gives you a good idea of the performance as a whole

- components of CPU time
	- program execution cycles
		- includes cache hit time
	- memory stall cycles
		- mainly from cache misses
- with simplifying assumptions:
- $\text{Memory stall cycles} = \frac{\text{MEmory accesses}}{\text{Program}} * \text{Miss rate} * \text{Miss penalty}$
- $=\frac{Instructions}{Program}*\frac{Misses}{Instruction}*\text{Miss penalty}$
- when CPU performance increases, miss penalties become more significant
- decreasomg base CPI means you spend a greater proportion of time on memory stalls
- increasing clock rate means memory stalls account for more CPU cycles
- can't neglect cache behavior when evaluating system performance