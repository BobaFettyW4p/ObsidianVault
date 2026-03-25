# Fundamentals of Quantitative Design and Analysis

### Class structure
> "Goal is the complement to high-performance computing. HPC wrote applications based on familiar algorithms (motifs). We though abotu hwo to have applications run efficiently on modern parallel hardware. Trusted design and the compiler to create efficient code. This goes the other direction. Take small artifacts and asks how the hardware sees them"

- every week, something is due
	- not so much coding, it's about understanding how they execute
- performance counters?
	- how many L1 cache misses, branch mispredictions, etc.
- did you give it a good effort?
- late work generally not accepted

PAPI - Performance Application Programming Interface

### Performance improvements over 65 years since first general-purpose electronic computer
- improvements in semiconductor technology
	- feature size, clock speed, lower votage
- Improvements in computer architecture
	- enabled by compilers -> no longer code in assembly
	- enabled by common OS: UNIX
	- lead to possibility of much simpler architectures (RISC)
- Together:
	- massive performance improvements
	- lightweight computers
	- productivity-based managed/interpreted programming languages
Fortran - 77; compilers - 88
- most stuff was done in assembly
- exabyte/exaflop systems are the largest currently in existence
L1 cache - 32 KB
L2 cache - 256 KB

### C to MIPS to Binary
- C is the "high level" language in this course
	- gets compiled into assembly by the compiler
		- makes performance analysis hard
			- have to look at assembly
# Don't compile without the -O2 flag

### Silicon Ingot to Computer Chip Production
- go learn about this

- Dennard scaling

### Current trends in architecture
- cannot continue to leverage Instruction-Level Parallelism (ILP)
	- single core performance improvement ended in 2003
- end of clock speed scaling
	- Moore's Law continues
	- Dennard scaling ended
		- constant power density with shrinking transistor size
	- Power considerations -> clock speed increases not possible with reduced feature size
	- ~15 years into multicore era

### Approaches to parallelism: Flynn's taxonomy
- single instrcution stream, single data stream (SISD)
- single instruction stream, multiple data streams(SIMD)
	- vector architectures
	- multimedia extensions
	- graphics processor units
- multiple instruction streams, single data stream (MISD)
	- no commercial implementation
- multiple instruction streams, multiple data streams (MIMD)
	- tightly-coupled
	- loosely-coupled

- quiz 1
	- 