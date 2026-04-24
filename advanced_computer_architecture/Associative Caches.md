- fully associative
	- allow a given block to go in any cache entry
	- requires all entries to be searched at once
		- $O(n)$ operation, where $n$ is the total number of entries in the cache
	- fewer evictions
	- can use any algorithm to determine what gets evicted
	- but this is very costly
- $n$-way set associative cache
	- each set has $n$ entries
	- the block number determines which set
		- $\text{(Block number) modulo (\# of Sets in cache)}$
	- search all entries in a given set at once
	- this requires $n$ comparators
		- which is less expensive than a fully associative cache
### Associativity Example
- compare 4-block caches
	- direct mapped, 2-way set associative, fully associative
- block access sequence: 0,8,0,6,8
- Direct mapped
![[Pasted image 20260408195833.png]]
- 2-way set associative
![[Pasted image 20260408195952.png]]
- fully associative
![[Pasted image 20260408200006.png]]
- a *compulsory miss* is a reference to a cache that has never been touched before
	- nothing has been loaded into the cache yet
		- it's impossible for there to be a hit
### How much associativity?
- increased associativity decreases miss rate
	- but with diminishing returns
- Simulation of a system with 64 KB D-cache, 16-word blocks, SPEC2000
	- 1 way: 10.3%
	- 2 way: 8.6%
	- 4 way: 8.3%
	- 8- 8.1%

### Replacement Policy
- direct mapped: no choice
- set associative
	- prefer non-valid entry, if there is one
	- otherwise, choose among entries
- Least Recently Used (LRU)
	- choose the one unused for the longest time
		- simple for 2 way, manageable for 4 way, too hard beyond that
- Random
	- gives approximately the same performance as LRU for high associativity

### Multilevel Caches
- we think of things in terms of fast and slow memory
	- not quite reality
- typically a level 2 cache, and often a level 3 cache
- the primary cache is attached to the CPU
	- small but fast
- The Level 2 cache services misses from the primary cache
	- larger and slower than primary cache, but faster than main memory
- Main memory services L-2 cache misses
	- most systems now include an L-3 cache
- a model with some fast memory and then much more slow memory will get you pretty far
	- multilevel cache models have a couple optimizations involved that improve on this
### Example
- given base CPI = 4, clock rate = 4 Ghz
- miss rate/instruction = 2%
- main memory access time = 100 ns
- with just primary cache
	- $\text{miss penalty }= 100\ ns/0.25\ ns = 100\ ns$
	- $\text{effective CPI} =1 + 0.02*400 = 9$
	- it's hard to get a miss rate below 2%, and yet we have this performance
- assume we add an L-2 cache
	- Access time = 5 ns
	- Global miss rate to main memory = 0.5%
	- Primary miss with L-2 hit
		- penalty = 5 n/0.25 ns = 20 cycles
	- CPI = 1 + 0.02 * 20 + 0.005 * 400 = 3.4
	- Performance ratio = 9/3.4 = 2.6
### Multilevel cache considerations
- Primary cache
	- focus on minimal hit time
- L-2 cache
	- focus on low miss rate to avoid main memory access
	- hit time has less overall impact
- Result
	- L-1 cache usually smaller than a single cache
	- L-1 block size smaller than L-2 block size
- 90% of what's interesting about caches can be seen in a single level cache
	- multilevel caches are more of an optimization
### Instructions with Advanced CPUs
- out of order CPUs can executed instructions during a cache miss
	- pending store stays in load/store unit
	- dependent instructions wait in reservation stations
		- independent instructions continue
- effect of a miss depends on program data flow
	- much harder to analyze
	- use system simulation

### Experimental study of memory (Membench)
- microbenchmark for memory system performance
- for array A of length L (4 kb to 8 MB, doubling each time)
	- for stride s from 4 bytes (1 word) to L/2 by 2x each time
		- time the following loop
		- (repeat many times and average)
			- for i from 0 to L by s
				- load A[i] from memory (4 bytes)
### Intrinsity FastMATH
- embedded MIPS processor
	- 12 stage pipeline
	- instruction and data access on each cycle
- split cache
	- separate I-cache and D-cache
	- each 16 KB: 256 blocks * 16 words/block
	- D-cache: write-through or write-back
- SPEC2000 miss rates
	- I-cache: 0.4$
	- d-cache: 11.4%
	- weighted average: 3.2%