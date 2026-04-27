- relationship between virtual memory and data and instruction caches
- implemented by most processors
	- **virtually indexed, physically tagged**
>Consider a 10 bit virtual address, with 5 bit virtual page number, 3 bit cache block and 2 bit cache byte offset
>in the example `1101001101`, the virtual page number i `11010`, the cache index is `011`, the cache offset is `01`

- imagine a virtual address system with two processes
	- process 1 has some page table
		- key point: every process has its own page table
			- this is the point
			- every process sees the same address space, the page table is how the system controls which process is allowed to write where
	- process two will have a different page table that maps the same virtual address to a different physical address
	- a modern computer allows multiple processes to run at once
		- otherwise they'd have to run serially

### Practice Problem
>Given a simple 4 entry TLB:

![[screenshots/Pasted image 20260419134125.png]]

- the TLB is fully associative
- pages are 4 KiB
- we will use the LRU policy for evictions
- here is the page table in memory:
![[screenshots/Pasted image 20260419134228.png]]

![[screenshots/Pasted image 20260419134330.png]]

![[screenshots/Pasted image 20260419134610.png]]

![[screenshots/Pasted image 20260419134747.png]]