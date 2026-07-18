### What do we mean by bandwidth?
- bandwidth always refers to a path
- examples:
	- CPU cores to CPU-attached memory
	- GPU compute units to GPU-attached memory
	- CPU to GPU (PCIe, NVLink)
- in this discussion:
	- CPU bandwidth = cores to system memory
	- GPU bandwidth = SMs to device memory
### DDR memory (typical CPU memory)
- DDR = Double Data Rate DRAM
- Characteristics:
	- external DIMMs
		- DIMM = Dual In-line Memory Module
		- a CPU is build with a fixed number of memory channels
		- you typically populate one DIMM per channel for best performance
			- can add a second DIMM per channel 
				- tradeoff: more capacity, less speed
			- can also leave channels unpopulated, which reduces available memory bandwidth
### HBM Memory (common on GPUs)
- HBM = High Bandwidth Memory
- Characteristics:
	- stacked DRAM dies
	- very wide interfaces
	- located close to processor (on package)
		- "on-package" means the memory and processor are assembled together inside the same physical package, connected by very short, very wide links, rather than being separate components connected across the motherboard
### Where is memory located?
![[screenshots/Pasted image 20260510110829.png]]

### DDR vs HBM: structural difference
- DDR scales horizontally
	- more DIMMs
	- more channels
- HBM scales vertically
	- stacked dies
	- wide buses
- key distinction
	- DDR optimizes capacity and cost
	- HBM optimizes bandwidth
### CPU vs GPU memory attachment
- CPU systems:
	- typically DDR
	- large memory capacity
	- shared across many cores
- GPU systems:
	- typically HBM
	- smaller capacity
	- very high bandwidth
- HBM is not directly tied to GPUs
	- some CPUs also include HBM
### Conceptual layout: CPU vs GPU memory
- CPU + DDR
	- cores
	- caches
	- memory controller external DDR DIMMs
	- Path:
		- cores -> caches -> controller -> DDR
- GPU + HBM
	- many compute units
	- on-package memory
	- very wide interface
	- Path:
		- SMs -> HBM (on package)
### Why GPUs have higher peak bandwidth
- hardware factors:
	- very wide memory interfaces (HBM)
	- many memory channels
	- shorter distance between memory and compute
- result:
	- much higher pin bandwidth than DDR systems
### Peak vs sustained bandwidth
- peak bandwidth:
	- theoretical maximum
	- based on interface width and frequency
- sustained bandwidth:
	- what a program actually achieves
	- depends on access pattern and concurrency
- key point:
	- hardware peak does not guarantee performance
### Representative bandwidth and capacity (context matters)
- these are exmaple configurations, not limits
![[screenshots/Pasted image 20260510111419.png]]
- CPU DDR capacity is scalable; bandwidth depends on channels, not total capacity
- GPU HBM capacity is fixed per device; bandwidth is much higher per device
- HBM-equipped CPUs show that bgandwidth depends on memory technology, not just CPU vs GPU
### STREAM vs. irregular access
- STREAM-like:
	- sequential access
	- predictable pattern
	- easy to prefetch
	- can approach peak bandwidth
- irregular access:
	- pointer chasing
	- graph traversal
	- random access
	- low effective bandwidth
### Why GPUs achieve high sustained bandwidth
- three main reasons:
	- massive concurrency
	- many outstanding memory requests
	- efficient access patterns (coalescing)
### Concurrency and outstanding requests
- GPUs
	- thousands of threads
	- many memory requests in flight
- CPUs
	- limited load/store queues
	- limited miss tracking (MSHRs)
- Result
	- GPUs keep memory system busy more easily
### Access patterns
- GPUs
	- coalesced accesses
	- large, aligned transactions
- CPUs
	- often irregular access
	- rely on caches and prefetching
- Result:
	- GPUs use bandwidth more efficiently for regular workloads
### Latency handling
- CPUs
	- try to reduce latency
	- caches, speculation, out-of-order execution
- GPUs
	- tolerate latency
	- switch between threads
- Result:
	- GPUs maintain throughput even with long memory delays
### Strategic design differences
- CPU design:
	- general purpose workloads
	- large memory capacity
	- irregular access patterns
- GPU design:
	- throughput-oriented workloads
	- regular, parallel access
	- maximize data movement
- the memory choice follows workload
### When GPUs do not achieve high bandwidth
- bandwidth drops when:
	- access is irregular
	- coalescing fails
	- parallelism is low
- Examples:
	- graph traversal
	- pointer chasing
	- sparse data structures
### CPU memory system: fewer request sources, scalable capacity
![[screenshots/Pasted image 20260510112004.png]]
- bandwidth scales mainly with channels; capacity scales mainly with DIMMs
### GPU memory system: many request sources, high-bandwidth memory
![[screenshots/Pasted image 20260510112039.png]]
- many SMs generate many concurrent requests to wide on-package HBM interfaces
### Why similar counts do not mean similar request rates
- a CPU core and a GPU SM are not equivalent request sources
![[screenshots/Pasted image 20260510112124.png]]
- even if a CPU has many cores and a GPU has a similar number of SMs, the GPU can usually generate many more concurrent memory requests
	- one reason GPUs can keep high-bandwidth HBM interfaces busy on regular workloads
### Request generation: CPU cores vs GPU SMs
- CPU core
	- one or a few instruction streams
	- limited load/store queue
	- limited miss tracking
	- fewer outstanding misses
- GPU SM
	- many resident warps
	- many threads
	- latency hidden by switching warps
	- many outstanding requests