- next project looks deeply at the performance of CPUs and GPUs
	- require a lot of reasoning in performance difference between the two architectures
### CPU vs GPU memory bandwidth
- DDR, HBM, and GPUs achieve higher bandwidth

#### What do we mean by bandwidth?
- always refers to a path
- ex:
	- CPU cores to CPU-attached memory
	- GPU compute units to GPU-attached memory
	- CPU to GPU (PCIe, NVLink)
- CPU bandwidth = cores to system memory
- GPU bandwidth = SMs to device memory

### DDR Memory (typical CPU memory)
- DDR = Double Data Rate DRAM
	- external DIMMs
	- relatively narrow channels
	- high capacity
	- lower cost per GB
- design goals:
	- scalable capacity general-purpose use
### HBM Memory (common on GPUs)
- HBM = high bandwidth memory
- Characteristics
	- stacked DRAM dies
	- very wide interfaces
	- located close to the processor
	- high bandwidth per watt
- Tradeoffs:
	- lower capacity
	- higher cost per GB
##### DDR vs HBM: structural difference
- DDR scales horizontally
	- more DIMMs
	- more channels
- HBM scales vertically
	- stacked dies
	- wide buses
- Key distinction
	- DDR optimizes capacity nad cost
	- HBM optimizes bandwidth

### conceptual layout: CPU vs GPU memory
- CPU + DDR
	- cores
	- caches
	- memory controller
	- external DDR DIMMs
	- Path:
		- cores -> caches -> controller -> DDR
- GPU + HBM
	- many compute units
	- on-package memory
	- very wide interfae
	- Path:
		- SMs -> HBM (on package)

### Why GPUs have higher peak bandwidth
- hardware factors
	- very wide memory interfaces (HBM)
	- many memory channels
	- shorter distance between memory and compute
- result:
	- much higher pin bandwidth than DDR systems
### STREAM vs irregular access
- two very different memory behaviors
	- STREAM-like
		- sequential access
		- predictable pattern
		- easy to prefetch
		- can approach peak bandwith
	- irrregular access:
		- pointer chasing
		- graph traversal
		- random access
		- low effective bandwidth