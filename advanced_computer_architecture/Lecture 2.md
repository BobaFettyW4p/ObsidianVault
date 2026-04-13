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
	- `TERM=xterm-256color sinteractive --account=mpcs52018 --partition=caslake --time=00:00:10`
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

### Cache Example Problem
>For a direct mapped cache design with a 32-bit address, the following bits of the addressed are used to access the cache
>Tag: 31-10
>Index: 9-5
>Offset: 4-0

>What is the cache block size (in words)?

- assuming it's a byte-addressable cache
	- cache block is given by number of offset bits
$$
5 \text{ total offset bits} = 2^5 \text{ bytes} = 32 \text{ bytes} = 8 \text{ words}
$$
- this assumes 4 bytes per word
	- which is what the book assumes

> How many entries does the cache have?

- we can tell this from the cache index

$$
5 \text{ index bits} = 2^5 = 32 \text{ bytes per cache line}  
$$

- this means there are $2^5 * 2^5$ total bytes of storage in the cache, which equals $2^{10} = 2 \text{ kibibytes}$ of data in the cache

>What is the ratio between total bits required for such a cache implementation over the data storage bits?

- the data storage bits = $2^{10}$ bytes = $8*2^{10}$ bits
- the cache will have 32 entries, each with a tag that must be stored
	- can't get around it
	- tag has 22 bits
		- $22 \text{ bits} * 32 \text{ slots} + 32 \text{ total valid bits} + 8*2^{10}$ is the total numbe rof bits for the cache
		- total number of bits for data: $8*2^{10}$
		- $\therefore,$
$$
\frac{22*32+32+8*2^{10}}{8*2^{10}} = 1.6875
$$

### Cache Example Problem 2
>Starting from power on, the following byte-addressed cache references are recorded

0
4
16
132
232
160
1024
30
140
3100
180
2180

> How many blocks are replaced?

- for each address, specify what cache line the address resides in, what is the offset, what is the memory, is it a hit?
	- address 0
		- look at 5 lower order bits
			- best way is to write it in binary
			- $0$ is $00000$
				- block will be 0, offset will be 0, mem will be `<0..31>`, it is not a miss
			- $4$ is $00100$
				- block will be 0, offset will be 4, mem will be `<0..31>`, it is a hit
					- hit because it was already loaded into cache
			- $16$ will be $100000$
				- block will be 0, offset will be 16, mem will be `<0..31>`, it is a hit
			- $132$ will be $10000100$
				- block will be 4, offset will be 4, mem will be `<128..159>`,  it is a miss
			- $232$ will be $11101000$
				- block will 7, offset will be 8, mem will be `<224.255>`, it is a miss

>What is the hit ratio?

-for the examples we did, since there were 5 entries, and 2 of them were hits, we would have a hit ratio of 2/5

>List the final state of the cache, with each valid entry represented as a record of `<index,tag,data>`

the final state would be 

- `<0, 0, 0>`, `<0, 4, 4`, `<0, 16, 16>`, `<4, 4, 132>`, `<7, 8, 232>`

