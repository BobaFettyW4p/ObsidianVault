## MIPS Assembler
- main takeaways from virtual memory:
	- when you look in the page translation table for a virtual address and want to find the corresponding physical address, one possibility is the valid bit set to 0
		- translation has not been made yet
			- lazy allocation

#### Proramming and CPUs
- programs written in a high-level language must be compiled
	- the result is an executable program file, containing CPU-specific machine language instructions
		- these instructions represent functions that can be handled by the processor
		- when you run the program, the instructions are loaded into memory and executed by the processor
	- a processor's instruction set is the boundary between software and hardware
#### Instruction sets
- two broad categories
	- older processors used complex instruction sets, or *CISC* architectures
	- many newer processors use reduced instruction sets, or *RISC* architectures

#### MIPS
- MIPS was one of the first RISC architectures
	- started about 35 years ago
- architecture is similar to that of other popular CPU designs
- uses three0address instructions for data manipulation
	- each ALU instruction contains a destination and two sources
