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
- Illustrative arithmetic intensity
	- $AI \approx \frac{7\ FLOPs}{16\ bytes}  = 0.44\ FLOPs/byte$
- Interpretation
	- higher arithmetic intensity than STREAM triad
	- better reuse moves the kernel to the right on the roofline
	- performance improves substantially, but still may be memory-bandwidth limited
- Key lesson
	- reuse increase arithmetic intensity
	- blocking and cache-aware optimization can shift performance toward the compute root
![[Pasted image 20260516085351.png]]

### Example: Sparse Matrix-Vector Multiply (SpMV)
- Per nonzero in the matrix:
	- 1 multiply +  1 add = 2 FLOPs
- Memory traffic includes:
	- matrix value ($val[j]$) = 8 bytes
	- column index ($colidx[j]$) = 4 bytes
	- vector access ($x[colidx[j]]$) = irregular/expensive
- Optimistic lower-bound arithmetic intensity:
	- ignoring much of the cost of irregular vector access:
		- $AI \equiv \frac{2\ FLOPs}{12\ bytes} = 0.17 FLOPs/byte$
- Reality:
	- irregular access to a vector often reduces effective locality
	- actual DRAM traffic may be significantly higher
	- effective arithmetic intensity may be lower than this estimate
- Conclusion:
	- strongly memory-bound
	- irregular access patterns reduce achievable bandwidth
	- simple roofline estimates may be optimistic
	- difficult to improve substantially without changing algorithm
![[Pasted image 20260516085909.png]]

### Example: Blocked DGEMM
- Matrix multiplication performs many operations on reused data
- blocking enables repeated reuse of A and B tiles from cache
- work scales as $O(B^3)$
- Data movement scales as $O(B^2)$
- There:
$$
AI = O(B)
$$
- Interpretation
	- arithmetic intensity grows with block size
	- effective reuse moves DGEMM far to the right on the roofline
	- well-optimized DGEMM is typically compute-bound
- Key Lesson
	- blocking transforms a memory-limited algorithm into a compute-efficient one
	- data locality is often the key to high performance
![[Pasted image 20260516092014.png]]

### Example: 1D Fast Fourier Transform (FFT)
- an N-point FFT performs: $O(N\ \log N)$ floating point work
- data movement depends strongly on implementation
- access patterns include strided and bit-reordered phases
- arithmetic intensity:
	- typically moderate (roughly order 1 FLOP/byte)
	- higher than streaming kernels, lower than blocked dense linear algebra
- Interpretation
	- FFT sits between memory bound and compute-bound extremes
	- performance depends heavily on cache behavior and implementation details
	- effective locality can matter as much as raw FLOP count
- Key lesson
	- arithmetic intensity alone does not tell the full story
	- access patterns strongly influence real performance
![[Pasted image 20260516092503.png]]

### Hierarchical Roofline
- the single DRAM roof is a simplification
	- real processors have multiple storage levels with very different bandwidths
- Examples
	- Registers
	- L1/L2/L3 cache
	- on-package high-bandwidth memory (e.g. GPUs)
	- DRAM
- Application locality matters
	- small working sets may fit in fast storage
	- larger working sets spill into slower levels
- each level defines its own roofline
	- different effective arithmetic intensity
	- different bandwidth ceiling
	- different attainable performance bound
- Key idea:
	- a kernel may be DRAM-bound, cache-bound, or compute-bound depending on problem size and implementation
- Real systems have multiple bandwidth ceilings
	- L1 cache
	- L2/L3 cache
	- high-bandwidth memory (if present)
	- DRAM
- Application locality determines which ceiling matters
	- small working sets may live in cache
	- larger working sets spill to slower memory
- a kernel may transition between regimes
	- compute-bound
	- cache-bandwidth bound
	- DRAM-bandwidth bound
- key idea
	- performance is limited by the lowest applicable ceiling



![[Pasted image 20260516093206.png]]

- construct a superposition of Roofline curves, one for each memory level (L1, L2, L3, DRAM, etc)
- for each level:
	- measure the bandwidth
	- estimate or measure the Arithmetic Intensity relative to that level
- a loop nest may have:
	- multiple arithmetic intensity values, depending on the level considered
	- multiple potential performance ceilings (e.g. compute bound, L1 bound, DRAM bound, etc)
> Performance is ultimately bounded by the lowest of these ceilings, the minimum of all applicable bounds

![[Pasted image 20260516093345.png]]

- Construct the superposition of the Rooflines
	- measure bandwidth
	- measure AI for each level of memory
	- although a loop nest may have multiple AI's and multiple bounds, performance is bound by the minimum
![[Pasted image 20260516093439.png]]

### Data, Instruction, Thread-Level Parallelism
- we have assumed one can attain peak flops with high locality
- in reality, this is premised on sufficient...
	- use special instructions (e.g. fused multiply-add)
	- vectorization (16 flops per instruction)
	- unrolling, out-of-order execution (hide FPU latency)
	- OpenMP across multiple cores
- Without these ...
	- peak performance is not attainable
	- some kernels can transition from memory-bound to compute-bound
	- in reality, DRAM bandwidth is often tied to DLP and TLP (single core can't saturate BW w/scalar code)