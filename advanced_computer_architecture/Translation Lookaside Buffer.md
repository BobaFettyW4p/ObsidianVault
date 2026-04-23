- the TLB acts like a cache for translation table
![[Pasted image 20260419123638.png]]

- a hit looks like:
	- the CPU issues a command on a virtual ddress, which is sent to a memory management unit
	- the memory management unit sends the virtual page number to the TLB cache
	- the TLB returns the page table entry to the memory management unit, which translates to the appropriate physical address to the regular memory
		- will then find the data via physical caching units
- on a TLB miss, the TLB returns a TLB miss
	- the page table entry must be sent to memory, where the page table entry will be loaded into the TLB, where the rest of the cycle will continue
![[Pasted image 20260419123904.png]]

- a TLB miss incurs an additional memory access
	- in practice, TLB misses are rare
		- locality is present to a greater degree as page sizes are large
![[Pasted image 20260419124202.png]]

![[Pasted image 20260419124610.png]]

- imagine we have a level 1 page table
	- each level 2 page points to an address in virtual memory
- each process has an exclusive virtual address space
	- one process cannot overwrite another's memory
	- if they need to share, map different virtual pages to the same physical page