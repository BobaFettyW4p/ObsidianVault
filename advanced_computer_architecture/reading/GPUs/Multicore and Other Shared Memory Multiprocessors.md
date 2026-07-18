- while hardware multithreading improved the efficiency of processors at modest, efficiently programming the increasing number of processor per chip has been a big challenge
- it's hard to rewrite old serial programs to run on parallel hardware
	- what can be done to simplify this task?
	- one answer was to provide a single physical address space that all processors share
		- this means programs don't have to concern themselves with where their data is, just that programs may be executed in parallel
		- all variables of a program can be made available at any time to any process
	- an alternative is to have a separate address space per processor that requires that sharing must be explicit
	- when the physical address space is common then the hardware typically provides cache coherence to give a common view of the shared memory
- a *shared memory multiprocessor (SMP)* is one that offers the programmer a single physical address space across all processors
	- this is nearly always the case for multicore chips
	- a more accurate term would be shared-address multiprocessor
- processors communicate through shared variables in memory, with all processors capable of accessing any memory location via loads and stores
	- these systems can run independent jobs in their own virtual address space, even if they share a physical address space
- single address space processors come in two styles
	- *uniform memory access (UMA) processors*
		- a multiprocessor in  which latency to any word in main memory is about the same no matter which processor requests the access
	- *nonuniform memory access (NUMA) processors*
		- a type of processor in which some memory accesses are much faster than others depending on which processor asks for which word
	- the programming challenges are harder for a NUMA processor than a UMA processor, but NUMA machines can scale to larger sizes and NUMAs can have lower latency to nearby memory
- processors operating in parallel will normally share data
	- they also need to coordinate when operating on shared data
		- one processor could start working on data before another is finished with it
		- called *synchronization*
			- when sharing is supported with a single address space, there must be a separate mechanism for synchronization
				- one approach uses a *lock* for a shared variable
					- only one processor at a time can acquire the lock
						- other processors interested in the shared data must wait until the original processor unlocks the variable
![[screenshots/Pasted image 20260510091320.png]]
### A simple Parallel Processing Program for a Shared Address Space
- suppose we want to sum 64,000 numbers on a shared memory multiprocessor computer with uniform memory access time
	- let's assume 64 processors
- first step is to ensure a balanced load per processor
	- split the set of numbers into subsets of the same size
		- we do not allocate the subsets to a different memroy space
			- there is a single memory space for this machine
				- we just give different starting addresses for each processor
					- $P_n$ is the number that identifies the processor, between 0 and 63
- all processors start the program by running a loop that sums their subset of numbers
```
sum[Pn] = 0
for (i = 1000*Pn; i < 1000*(Pn+1); i+=1)
	sum[Pn] += A[i]; /* sum the assigned areas*/
```
- the next step is to add these 64 partial sums
	- called a *reduction*
	- half the processors add pairs of partial sums, then a quarter add pairs of the new partial sums, so on and so forth, like so:
![[screenshots/Pasted image 20260510091940.png]]

### Hardware/Software Interface
- given the long-term interest in parallel programming, there have been many attempts to build parallel programming systems
	- a popular example is *OpenMP*
		- API along with a set of compiler directives, environment variables, and runtime library routines that can extend standard programming languages
- most C compilers already have support for OpenMP
	- to use OpenMP with the UNIX C compiler:

```
cc -fopenmp foo.c
```

 - OpenMP extends C using *pragmas*, which are commands to the C preprocessor
- to set the number of processor we want to use to be 64:
```
#define P 64 /*define a constant that we'll use a few times */
#pragma omp parallel num_threads(P)
```

- this would set the runtime library to use 64 parallel threads
- to turn the sequential for loop into a parallel for loop that divides the work equally among all threads:

```
#pragma omp parallel for
for (Pn = 0; Pn < P; Pn += 1)
    for (i=0; 1000*Pn; i < 1000*(Pn+1); i+=1)
        sum[Pn] += A[i]; /* sum the assigned areas */
```

- to perform the reduction, we use another command that tells OpenMP what the reduction operator is and what variable you need to use to place hte result

```
#pragma omp parallel for reduction(+ : FinalSum)
for (i=0; i< P; i+=1)
```

- it is now up to the OpenMP library to find efficient code to sum 64 numbers efficiently using 64 processors
- OpenMP is very easy to write simple parallel code, but it's not helpful with debugging

