### Performance Modeling
- Why use performance models or tools?
	- identify performance bottlenecks
	- motivate software optimizations
- Assess whether further optimization is likely to be worthwhile
	- assess performance relative to architectural limits
	- motivate need for algorithmic changes
- Estimate performance trends on future architectures
	- set realistic expectations for future procurements
	- support HW/SW co-design to ensure future architectures match application needs
>Models are simplifications; their value is in guiding reasoning, not exact prediction

- Performance Models/Simulators
	- historically, many performance models and simulators explicitly tracked operation latencies to predict execution time
		- e.g. cycle counting
	- modern architectures employ multiple latency-hiding mechanisms:
		- out of order execution
			- discover independent work dynamically
		- hardware prefetching
			- speculatively bring data closer
		- caches
		- massive thread parallelism
			- other threads execute while others wait on memory
- for many regular numerical workloads, enough concurrency exists that throguhput often becomes a more useful performance lens than raw latency
	- some workloads still remain latency bound

### Not All Workloads Fit the Throughput Model
- some workloads expose memory latency directly rather than hiding it
- Examples:
	- pointer chasing/linked data structures
	- graph traversal
	- sparse indirect memory access
	- randomized lookup-heavy algorithms
	- synchronization-heavy parallel codes
- Why?
	- little spatial locality
	- poor predictability
		- prefetchers struggle to be useful
	- limited independent work for OoO execution
	- insufficient concurrency to hide latency
- Result:
	- performance may be latency-limited rather than bandwdith-limited
	- simple roofline reasoning can become misleading
## The Roofline Model
- Throughput-focused model
	- model's performance in terms of sustained execution rate rather than individual operation latency
- Portable conceptual framework
	- applicable across CPUs, GPUs, and other throughput-oriented architectures
- Core idea:
$$
P \leq min(P_{peak} , I*B)
$$
- where:
	- $P_{peak}$ is the peak compute throughput
	- $I$ is the arithmetic intensity
	- $B$ is the attainable memory bandwidht
- requires machine characterization
	- compute throughput
	- memory bandwidth
- requires application characterization
	- operation count
	- memory traffic
	- arithmetic intensity
- it is an idealized model
	- assumes sufficient concurrency, effective overlap, and reasonably attainable bandwidth
### Interpreting the (DRAM) Roofline Model
- ideal goal: achieve peak FLOP/s performance
- in practice: finite data reuse and limited memory bandwidth constrain performance
- model setup: assume an idealized processor and cache behavior
- plotting
	- use Arithmetic Intensity (AI) on the x-axis
		- $AI = FLOPs/Bytes$ (from DRAM)
- Performance bound:
	- $\text{Attainable FLOP/s} = min(\text{Peak FLOPs}, AI \times \text{Peak GB/s})$
	- log-log scale: enables easy extrapolation and visualization of trends
- Interpretation:
	- kernels with AI below the machine balance point are memory-bound
	- kernels with higher AI may become compute-bound
![[Pasted image 20260515073903.png]]

###  Machine Balance and Memory-Bound Kernels
- many important kernels ahve low arithmetic intensity
	- STREAM Triad: $Z[i] = X[i] + \alpha Y[i]$
- Work per iteration:
	- 2 FLOPs: 1 multiply, 1 add
- Data movement per iteration:
	- Read $X[i]$ -> 8 bytes
	- Read $Y[i]$ -> 8 bytes
	- Write $Z[i]$ -> 8 bytes
	- Total: 24 bytes
- Arithmetic intensity
	- $AI = \frac{2\ FLOPs}{24\ bytes} = 0.083\ FLOPs/byte$
- Interpretation
	- this is far to the left of the machine balance point
	- performance is constrained by DRAM bandwidth, not compute throughput
	- even highly-optimized implementations will remain memory-bandwidth bound
- General less
	- many streaming and stencil style kernels behave similarly
	- improving performance often requires increasing data reuse, not just optimizing arithmetic
![[Pasted image 20260515075127.png]]

### Example: 7-Point Constant-Coefficient Stencil
- Computation per grid point
	- 7 floating point operations
	- 7 neighbor reads +  1 write
- Data Reuse matters
	- neighbor values are reused across adjacent stencil updates
	- effective DRAM traffic can be much lower than the naive memory reference point