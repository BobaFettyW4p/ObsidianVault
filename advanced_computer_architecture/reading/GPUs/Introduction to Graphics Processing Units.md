- the original justification for adding SIMD instructions to existing architectures was that many microprocessors were connected to graphics displays
	- an increasing fraction of processing time was used for graphics
- several key characteristics that CPUs and GPUs differ:
	- GPUs are accelerators that supplement a CPU, so they don't need to be able to perform all the functions of a CPU
		- this allows them to dedicate all their resources to graphics
	- GPU problem sizes are typically hundreds of megabytes to gigabytes
- these differences have led to different styles of architecture:
	- GPUs do not rely on multilevel caches to overcome long latency
		- instead, they rely on hardware multithreading to hide the latency to memory
			- between the time of a memory request and the time data arrives, the GPU executes hundreds or thousands of threads that are independent of that request
	- GPU memory is oriented towards bandwidth rather than latency
		- there are special graphics DRAM chips for GPUs
			- wider and have higher bandwidth than DRAM chips for CPUs
		- GPU memories have traditionally had smaller main memories than conventional microprocessors
	- GPUs rely on many threads to deliver good memory bandwidth, and so they can accommodate many parallel processors (MIMD) as well as many threads
		- each GPU processor is more highly multi threaded than a typical CPU, and have more processors
#### Hardware/Software Interface
- although GPUs were designed for a narrower set of applications, some programmers wondered if they could specify their applications in a way to tap the high potential performance of GPUs
	- eventually, C-inspired programming languages let programmers write programs directly for GPUs
		- an example is NVIDIA CUDA (Compute Unified Device Architecture)
			- the unifying theme of all these forms of parallelism is the *CUDA Thread*
### An Introduction to the NVIDIA GPU Architecture
- similar to vector architectures, GPUs only work well with data-level parallel problems
	- both styles have gather-scatter data transfer, and GPU processors have even more registers than vector processors
	- unlike vector architectures, GPUs rely on hardware multithreading within a single multi-threaded SIMD processor to hide memory latency
		- a multithreaded SIMD processor is similar to a vector processor, but a SIMD processor has many parallel functional units instead of a few deeply pipelined units
- a GPU contains a collection of multithreaded SIMD processor
	- a GPU is a MIMD composed of multithreaded SIMD processors
		- NVIDIA has four implementations of the Fermi architecture at different price points
- to provide transparent scalability across models of GPUs with differing number of multithreaded SIMD processors, the Thread Block Scheduler hardware assigns blocks of threads to multithreaded SIMD processors

![[screenshots/Pasted image 20260510104659.png]]

- the machine object that the hardware creates, manages, schedules, and executes is a *thread of SIMD instructions*
	- also called a *SIMD thread*
	- it's a traditional thread, but it contains exclusively SIMD instructions
		- SIMD threads have their own program coutners and run on a multithreaded SIMD processor
	- the *SIMD thread scheduler* includes a controller that lets it know which threads of SIMD instructions are ready to run, and then it sends them off to a dispatch unit to be run on the multithreaded SIMD processor
		- identical to a hardware thread scheduler in a traditional multithreaded processor, except it schedules threads of SIMD instructions
- Therefore, GPU hardware has two levels of hardware schedulers:
	- the Thread Block Scheduler assings blocks of threads to multithreaded SIMD rpocessors
	- the SIMD Thread Scheduler within a SIMD processor, which schedules when SIMD threads should run
- The SIMD instructions of these threads are 23 wide, so each thread of SIMD would comptuer 32 of the elements of the computation
	- as the thread consists of SIMD isntructions, the SIMD processor must have parallel functional units
		- called *SIMD Lanes*
### NVIDIA GPU Memory Structures
- the on-chip memory that is local to each multithreaded SIMD processor is called *Local Memory*
	- shared by the SIMD Lanes within a muiltithreaded SIMD processor
		- this memory is not shared between multithreaded SIMD processors
			- the off-chip DRAM shared by the whole GPU and all thread blocks *GPU Memory*
- as opposed to relying on large caches to contain the working sets of an application, GPUs traditionally use smaller streaming caches and rely on extensive multitherading of SIMD instructions to hide the long latency to DRAM, as their working sets can be hundreds of megabytes
	- thus, working sets generally do not fit within the last level cache of a multicore microprocessor
	- GPU designers tend to prefer to use chip area to add more processors as opposed to a larger cache
![[screenshots/Pasted image 20260510105701.png]]

### Putting GPUs into Perspective
- at a high level, multicore computers with SIMD instruction extensions do share similarities with GPUs

![[screenshots/Pasted image 20260510105838.png]]

- both are MIMDs whose processors use SIMD lanes, although GPUs have more processors and more lanes
- both use hardware multithreading to improve processor utilization, although GPUs have support for many more threads
- both use caches, although GPUs use smaller streaming caches and multicore CPUs use large multilevel caches
- both use a 64-bit address space, although the physical main memory is much smaller in GPUs
- while GPUs suppoer memory protection at the page level, they do not yet support demand paging
- CPUs and GPUs do not share a "common ancestor" and so may not share the same terms
![[screenshots/Pasted image 20260510110326.png]]
