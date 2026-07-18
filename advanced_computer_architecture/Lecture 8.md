### The Roofline Model
- Why use performance models or tools?
	- identify performance bottlenecks
	- motivate software optimizations
- assess whether further optimization is likely to be worthwhile
	- assess performance relative to architectural limits
	- motivate need for algorithmic changes
- estimate performance trends on future architectures
	- set realistic expectations for future procurements
	- support HW/SW co-design to ensure future architecture match application needs
> Models are simplifications; their value is in guiding reasoning, not exact prediction

> "All models are wrong, some models are useful"

- for HPC code, nobody cares about 20%
- a TFLOP or Terraflop represents the ability to process one trillion floating point operations per second
- for a square, $n \times n$ matrices using the standard matrix multiplication algorithm the total number of operations is $O(n^3)$
	- a matrix multiply with $n = 10^4$ rows will require $O(n^3) = 10^{12}$ operations, about 1 TFLOP
### Not all workloads fit the throughput model
- some workloads expose memory latency directly rather than hiding it
- Ex:
	- pointer chasing/linked data structures
	- graph traversal
	- sparse indirect memory access
	- randomized lookup-heavy algorithms
	- synchronization-heavy parallel codes
- Why?
	- little spatial locality
	- poor predictability
		- prefetchers tend to struggle
	- limited independent work for OoO exection
## Roofline Model
- it's a throughput-focused model
	- model's performance in terms of sustained execution rate rather than individual operation latency
- portable conceptual framework
	- applicable across CPUs, GPUs, and other throughput-oriented architectures
- you want to be CPU-bound, not memory bound
- Core idea:
	- $P \leq (P_{peak} I * B)$
		- $P_{peak}$: peak compute throughput
		- $I$: arithmetic intensity
		- $B$: attainable memory bandwidth
- requires machine characterization
	- compute throughput
	- memory bandwidth
- requires application characterization
	- operation count
	- memory traffic
	- arithmetic intensity
- Idealized model
	- assumes sufficient concurency, effective overlap, and reasonably sustainable bandwidth

### Interpreting the DRAM Roofline model
- ideal goal: achieve peak FLOP/s performance
- in practice: Finite data reuse and limited memory bandwidth constrain performance
- model setup: assume idealized processor and cache behavior
- Plotting: use Arithmetic Intensity ($AI$) on the X-axis
	- $AI = FLOPs/bytes$ (from DRAM)
- Performance bound:
	- $\text{Attainable FLOP/s} = min(\text{Peak FLOPs/} AI \times \text{Peak GB/s})$
	- log-log scale: enables easy extrapolation and visualization of trends
- Interpretation:
	- kernels with AI below the machine balance point are memory-bound
	- kernels with higher AI may become compute bound