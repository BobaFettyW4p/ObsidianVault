# Virtual Memory
-  caches provide fast access to recently used portions of memory
	- simialrly, the main memory can act as a "cache" for the secondary storage, typically implemented with magnetic disks
		- this technique is called *virtual memory*
- two major motivations for virtual memory historically
	- allow for efficient and safe sharing of memory among multiple programs
		- i.e. the memory needed for multiple VMs for cloud computing
		- main reason that still applies today
	- remove the programming burdens of a small, limited amount of main memory
- to allow multiple VMs to share the same memory, we must be able to protect VMs from each other
	- ensure a program can only read and write to the portions of memory assigned to it
- main memory only has to contain the active portions of many VMs
	- just as the cache only contains the the active portion of one program
	- this allows for the principle of locality  to enable virtual memory
		- virtual memory allows us to efficiently share the processor as well as the main memory
- we can't know which VM will share memory with other VMs when they are compiled
	- the VMs sharing memory will change dynamically while they are running
		- thus, we would like to compile each program intos own *address space*
			- *address space* - a separate range of memory locations available only to a specific program
- virtual memory implements the translation of a program's address space into *physical addresses* - an address in main memory
	- this translation process enforces *protection* of a program's address space from other virtual machines
		- *protection* - a set of mechanisms for ensuring that multiple processes sharing the processor, memory, or I/O devices cannot interfere with each other by reading or writing each other's data
- the second motivation for virtual memory is to allow a single user program to exceed the size of primary memory
	- previously, if a program became too large for memory, there was no way to make it work besides making the program fit
		- programmers divided programs into pieces and identified the pieces that were mutually exclusive
			- these *overlays* were loaded or unloaded during execution, with the programmer ensuring the program never tried to access an outlay that wasn't loaded into memory, and that the overlay never exceeded the size of the memory
				- calls between procedures in different modules would lead to the overlaying of one module with another
			- this was a significant burden on programmers
				- virtual memory was created to solve this problem
					- automatically manages the two levels of the memory hierarchy represented by main memory
- although virtual memory and caching use similar concepts, their different historical roots had led to the use of different terminology
	- a virtual memory block is called a *page*
	- a virtual memory miss is called a *page fault*
		- *page fault* - an event that occurs when an accessed page is not present in main memory
	- with virtual memory, the processor produces a *virtual address*
		- *virtual address* - an address that corresponds to a location in virtual space and is translated by address mapping to a physical address when memory is accessed
			- a physical address can be used to access main memory
				- this process is called *address mapping* or *address translation*
![[screenshots/Pasted image 20260414210259.png]]
- today, the two memory hierarchy levels controlled by virtual memory and typically DRAMs and flash memory in mobile devices and DRAMs and magnetic disks in servers
- virtual memory simplifies loading the program for execution by providing *relocation*
	- *relocation* maps the virtual addresses used by a program to different physical addresses before the addresses are used to load the program in main memory
		- today, all virtual memory systems relocate the program as a set of fixed size blocks
			- this eliminates the need to find a contiguous block of memory to allocate to a program
				- the OS only needs to find a sufficient number of pages in main memory
- virtual memory is broken into a *virtual page number* and a *page offset*
	- the physical page number constitutes the upper portion of the physical address
	- the page offset constitutes the lower portion
	- the number of bits in the page offset field indicates the page size
		- the number of pages addressable with the virtual address need not match the number of pages addressable with the physical address
			- having a larger number of virtual pages than physical pages is the basis for the illusion of essentially unbounded amounts of virtual memory
![[screenshots/Pasted image 20260414210644.png]]

- many design changes in virtual memory systems are motivated by the high cost of a page fault
	- a page fault to disk can take millions of clock cycles to process
		- this leads to several key decisions:
			- pages should be large enough to amortize high access time
				- 4KiB to 16 KiB is typical today
			- organizations that reduce page fault rate are attractive
				- primary technique is to allow fully associative placement of pages in memory
			- page faults can be handled in software because the overhead will be small compared to disk access time
				- software can use clever algorithms to place pages
					- even small reductions in miss rate pay for the cost of implementing these algorithms
			- write-through will nto work for virtual memory
				- writes take too long
				- virtual memory uses write back
- virtual memory was originally invented so many programs could share the computer as part of a timesharing system
##### Segmentation
- in addition to paging, there is a variable-size block scheme called *segmentation*
	- in segmentation an address consists of two parts
		- segment number
			- a segment number is mapped to a physical address
		- segment offset
			- added to the segment number to find the actual physical address
	- because a segment can vary in size, a bounds check is needed to make the offset resides within the segment
- a major use of segmentation is to support more powerful methods of protecting and sharing address space
- a major disadvantage is that it splits the address space into logically separate pieces that must be manipulated as a two part address
- segments have also been used to extend the address space without changing the word size of the computer
	- these attempts have largely been unsucessful because of the awkwardness and performance penalties inherent in a two part address
- many architectures divide the address space into large fixed-size blocks that simplify protection and increase the efficiency of paging
	- these divisons are often called segments, but this mechanism is much simpler than variable block size segmentation
### Placing a Page and Finding It Again
- due to the incredibly high page fault penalty, designers aim to reduce page faults by optimizing page placement
	- if a virtual page can be mapped to any virtual page, the OS can replace any page it wants when a page fault occurs
		- the OS can use sophisticated algorithms to try and choose a page that will not be needed for a long time
- the difficulty in using fully associative placement is in locating an entry
	- it can be anywhere in the upper level of the hierarchy
		- a full search is impractical
	- page are located using a table that indexes the pages and resides in memmory
		- called a *page table*
			- page tables are indexed with the page number from the virtual address to discover the corresponding physical address
- the page table, along with the program counter and the registers, specifies the state of a virtual machine
	- if we want to allow another virtual machine to use the processor, this state must be saved so it can be restored later
		- after restoring the state, the virtual machine can resume execution
			- this state is referred to as a *process*
				- it is considered *active* when it is in possession of the processor
					- otherwise it is considered *inactive*
				- processes are activated by the OS by loading its state
					- the program counter will initiate execution at the value of the saved program coutner
- the processor's address space (and thus, all the data it can access in memory) is defined by its page table
	- as opposed to loading the netire page table, the OS can load the page table register to the page table of the process it wants to activate
![[screenshots/Pasted image 20260415052533.png]]
### Page Faults
- if the valid bit for a virtual page is off, a page fault occurs
	- the OS must be given control, and the transfer is done with the exception mechanism
		- once the OS gets control, it must find the page and decide where to place it in main memory
- the virtual address alone does not immediately tell us where the page is on disk
	- we must keep track of the location on disk of each page in virtual address space
- because we don't know ahead of time when a page in memory will be replaced, the operating system usually creates the space on flash memory or disk for the pages of a process when it creates a process
	- this space is called *swap space*
	- the OS also creates a data structure to record where each virtual page is stored on disk
		- may be part of the page table or may be an auxiliary data structure structured the same way as the page table
- the OS also creates a data structure that tracks with processes and which virtual addresses use each physical page
	- when a page fault occurs, if all the pages in main memory are in use, the OS must choose a page to replace
		- in order to minimize the number of page faults, most OSes will utilize LRU
![[screenshots/Pasted image 20260415054509.png]]

- a completely accurate LRU scheme is too expensive
	- requires updating a data structure on every single memory reference
- most OSes just approximate LRU by tracking which pages have been recently used and which have not
	- some computers use a *reference bit* which is set whenever a page is accessed, which the OS will periodically clear
		- allows the OS to see which pages have been touched in a set time period
> With a 32 bit virtual address, 4 KiB pages, and 4 bytes per page table entry, we can compute the total page table size

$$
\text{Number of page table entries} = \frac{2^{32}}{2^{12}} = 2^{20}
$$
$$
\text{Size of page table} = 2^{20} \text{ page table entries} * 2^2\frac{\text{bytes}}{\text{page table entry}} = 4 \text{ MiB}
$$
- thus, we would need to use 4 MiB of memory for each program in execution at any time
	- not so bad for a single process, but what if there are hundreds of them, each with their own page table?
- a range of techniques are used to reduce the amount of storage required for a page table
	- the simplest technique is to keep a limit register that restricts the size of the page table for a given process
		- if the virtual page number becomes larger than the contents of the limit register, entries must be added to the page table
			- allows the page table to grow as the process consumes more space
				- the page table will only be large if the process is using many pages of virtual address space
					- requires that address space expands in only one direction
	- most languages require two areas whose size is expandable
		- one holds the stack, and the other holds the heap
		- it's convenient to divide the page table and let it grow from the highest address down, as well as from the lowest address up and meet in the middle
		- two separate page tables and two separate limits
			- two page tables breaks address space into two segments
			- the higher order bit specifies the segment, with each segment able to be as big as 1/2 the address space
			- a limit register for each segment specifies the current size of the segment, which grows in units of pages
			- this is used by many architectures, including MIPS
	- another approach is to reduce the page table size by using a hashing function to the virtual address
		- the page table only need be as large as the size of the number of physical pages
			- referred to as an *inverted page table*
			- more complex lookup process
	- multiple levels of page tables can also reduce the total amount of page table storage
		- first level maps large fixed size blocks of virtual address space
			- these blocks are sometimes called segments, and this first level mapping is called a segment table
				- each entry in the segment table indicates whether any pages in that segment are allocated
					- points to a page table for that segment
				- address translation happens by looking in the segment table using the higher-order bits of the address
					- if it's a match, the next set of higher order bits can be used to index the page table
					- this scheme allows the address space to be used in a sparse fashion without allocating the entire page table
						- useful with very large address spaces and in software systems that require noncontiguous allocation
							- main disadvantage: there is a mor complex address translation process
	- to reduce actual memory used by page tables, many modern systems allow the page table to be paged
		- uses the same basic principles of virtual memory and allows page tables to reside in virtual address space
			- some small issues that need to be accounted for
				- how do you prevent a page fault from recursing endlessly?
### What about writes?
- the difference between accessing cache and main memory is tens to hundreds of cycles
	- a write-through scheme can be used, but we need a write buffer to hide the latency of the write from the processor
		- in virtual memory, writes to disk can take millions of cycles
			- thus, a write buffer would be completely impractical
				- write back systems are more or less mandatory
					- also has another advantage
						- because disk transfer time is much smaller than access time, copying an entire page is much more efficient than writing individual words back to the disk
							- still costly, though
							- we would like to know whether a page needs to be copied back when we choose to replace it
								- done by adding  a *dirty bit* to the page table
									- when any changes are made, it's flipped, and then the system knows it must write the page back when discarded
### Making Address Translation Fast: the TLB
- since page tables are stored in memory, every memory access by a program can take at least twice as long
	- one memory access to obtain the physical address
	- a second access to actually get the data
	- to fix this, we need to leverage locality in the page table
		- when a virtual page number is translated, it will probably need to be accessed again soon
	- thus, modern processors include a special cache that tracks recently used translations 
		- traditionally referred to as the *translation-lookaside buffer (TLB)*
			- more accurate to call it as translation cache
![[screenshots/Pasted image 20260415192704.png]]

- each tag entry in the TLB holds a portion of the virtual page number, and each data entry of the TLB holds a physical page number
	- as we access the TLB instead of the page table, we need to incldue other status bits
		- dirty and reference bits
- on every reference, we look up the virtual page number in the TLB
	- if a miss occurs, we must determine if it's a page fault or just a TLB miss
		- if the page exists in memory, the TLB miss simply indicates only that the translation is missing
			- can be rectified by loadind the translation into the TLB and trying again
		- if the page does not, then the TLB needs to indicate a true page fault
		- as the TLB has many fewer entries than the number of pages in main memory, TLB misses should be much more frequent than true page faults
- TLB misses can be handled in either hardware or software
	- in practice, there is little performance difference between the two
- after a TLB miss occurs and and the missing translation has been received, we have to select a TLB entry to replace
	- as the reference and dirty bits are contained in the TLB entry, we need to copy these bits back to the page table entry
		- these are the only portion of the TLB entry that can be changed
	- write back is very efficient as we expect the TLB miss rate to be small
- some typical TLB settings:
	- TLB size: 16-512 entries
	- block size: 1-2 page table entries (typically 4-8 bytes each)
	- hit time: 0.5-1 clock cycle
	- miss penalty: 10-100 clock cycles
	- miss rate: 0.01%-1%
- designers have used a wide variety of associativities in TLBs
	- some use small, fully associative TLBs
		- a fully associative mapping has a lower miss rate
			- since the TLB is small, the cost of a fully associative mapping is not too high
	- others use large TLBs with small associativity
		- choosing the entry to replace becomes tricky as LRU is too expensive
		- makes TLB misses more frequent, and thus must be handled more cheaply
### The Intrinsity FastMATH TLB
- the memory system uses 4 KiB pages and a 32-bit address space
	- the virtual page number is 20 bits long
- the physical address is the same as the virtual address
- the TLB contains 16 entries, is fully associative, and is shared between instruction and data references
	- each entry is 64 bits wide and contains a 20 bit tag
- uses software to handle TLB misses
- when a TLB miss occurs, MIPS saves the page number of the reference in a special register and generates an exception
	- this exception invokes the OS, which handles the miss in software
- to find the physical address of the missing page, the TLB miss routing indexes the page table using the page number of the virtual address and the page table register, which indicates the starting address of the active process page table
- the OS then uses a special set of system instructions ot update the TLB
	- the OS places the physical address from the page table into the TLB
	- a TLB miss takes about 13 clock cycles
- there is an extra complication for write requests
	- the write access bit in the TLB must be checked
		- this bit prevents the program from writing into pages it only has read access to
### Integrating Virtual Memory, TLBs, and Caches
- virtual memory and cache systems work together as a hierarchy
	- data cannot be in the cache unless it is present in main memory
- the OS maintains this hierarchy by flushing any page from the cache that page is migrated to disk
	- similarly, the OS modifies the page tables and TLB, so an attempt to access any data on the migrated page will generate a page fault
- under ideal circumstances, a virtual address is translated by the TLB and sent to the cache where the appropriate data is found, retreived and sent back to the processor
- in the worst case, a reference can miss in all 3 components: TLB, page table, and cache
![[screenshots/Pasted image 20260415210852.png]]

- in a memory hierarchy like the above, a memory reference can encounter three different types of misses: a TLB miss, a page fault, and a cache miss
![[screenshots/Pasted image 20260415211010.png]]

![[screenshots/Pasted image 20260415210908.png]]

- figure 5.32 assumes all memory addresses are translated to physical addresses before the cache is accessed
	- the cache is *physically indexed and physically tagged*
		- both the cache index and tag are physical addresses, as compared to virtual
		- the amount of time to access memory must accomodate both a TLB and cache access
			- these accesses can be pipelined
- the processor can also index the cache with an address that is completely or partially virtual
	- called a *virtually addressed cache*
		- uses tags that are virtual addresses
			- virtually indexed and virtually tagged
			- the TLB is unused during normal cache access
			- the TLB is thus removed from the critical path, which reduces cache latency
- when the cache is accessed with a virtual address and pages are shared between processes, there is the possibility of *aliasing*
	- *aliasing* - a situation in which two addresses access the same object; it can occur in virtual memory when there are two virtual addresses for the same physical page
	- aliasing occurs when the same object has two names, e.g. two virtual addresses for the same page
		- a word on such a page may be cached in two different location, each corresponding to a different physical address
- a common compromise are caches that are virtually indexed but use physical tags
	- virtually indexed but physically tagged
	- attempt to achieve the performance of virtually indexed caches with the architecturally simpler advantages of a physically addresses cache
### Implementing Protection with Virtual Memory
- the most important function of virtual memory is to allow sharing of a single main memory by multiple processes
	- this also provides memory protection amongst these processes and the operating system
		- the protection mechanism must ensure that renegade processes are not allowed the write into the address space of another user process
- to enable the OS to implement virtual memory protection, the hardware must provide three basic capabilities:
	- support at least two modes that indicate whether the running process is a user process or an OS process
		- called a *supervisory* process, a *kernel* process, or an *executive* process
	- provide a portion of the processor state that a user process can read but not write
		- includes the user/supervisor mode bit
			- dictates what mode the processor is in, the page table pointer, and the TLB
				- writing is only avaiable from special instructions only available in supervisor mode
	- provide mechanisms where the processor can go from user mode to supervisor mode and vice versa
		- the first direction is provded by a *system call* exception, generally implemented as a special instruction
			- the opposite direction is generally handled by a *return from exception (ERET)* instruction
- we want to prevent a process form reading the data of another process
	- each process has its own virtual address space
		- if the OS keeps the pages tables organized so independent virtual pages map to disjoin physical pages, this will be maintained
		- this requires that a user cannot modify the page mapping on their own
			- requires the OS to assist any processes that want to share information with each other
- *context switch* - a changing of the internal state of the processor to allow a different process to use the processor that includes saving the state needed to return to the currently executing process
### Handling TLB misses and page faults
- the translation of virtual to physical addresses with a TLB is straightforward, but handling TLB misses and page faults is more complex
	- a TLB miss occurs when no entry in the TLB matches a virtual address
		-  TLB miss can indicate one of two possibiltiies:
			- the page is present in memory, and we need to create the missing TLB entry
			- the page is not present in memory, and we need to transfer control to the OS to deal with a page fault
- MIPS traditionally handles a TLB miss in software
	- brings the page table in from memroy and re-executes the instruction that caused the miss
		- it will get a TLB hit
- handling a TLB miss or page fault requires using the exception mechanism to interrupt the active process
	- this transfers control to the OS and laters resumes execution of the interrupted process
- in order to restart the instruction later, the program counter of the instruction that caused the page fault must be saved
	- a TLB miss or page fault exception must be asserted by the end of the same clock cycle that the memory access occurs
		- the next clock will begin exception processing rather than continue normal instruction execution
- between when we begin executing the exception handler in the OS and the time the OS has saved the state of the process, the OS is particularly vulnerable
	- if another exception occured, the control unit would overwrite the exception program counter, making it impossible to return to the instruction that caused the page fault
		- can be avoided by providing the ability to *disable* and *enable* exceptions
			- when an exception first occurs, the processor sets a bit that disables all other exceptions
				- the OS will then set aside enough state to recover if another exception occurs
- Once the OS knows the virtual address that cause the page fault, it must complete three steps
	- look up the page entry using the virtual address and find hte location of the referenced page on disk
	- choose a physical page to replace
		- if it's dirty, it must be written to disk first
	- start a read to bring the referenced page from disk into the chosen physical page
		- will take millions of clock cycles
![[screenshots/Pasted image 20260415215626.png]]

- when the read of the page from disk is complete, the OS can restore the state of the process that initially caused the page fault and execute the instruction that returns from the exception
	- the process will re-execute the instruction that faulted, access the requested page successfully, then continue execution
- page fault exceptions for data accesses are difficult to implement properly in a processor because of three characteristics
	- they occur in the middle of instructions
	- the instruction cannot be completed before handling the exception
	- after handling the exception, the instruction must be restarted as if nothing had occurred
- making instructions *restartable*, so an exception can be handled and the instruction later continued, is relatively easy in MIPS
	- each instruction writes only one data item and this write occurs at the end of the instruction cycle
		- we can simply prevent the instruction from completing and restart the instruction at the beginning
- in MIPS, when a TLB miss occurs, the MIPS hardware saves the page number of the reference in a special register called `BadVAddr` and generates an exception
	- the exception invokes the OS, which handles the miss in software
		- control is transferred to the miss *handler*, which is responsible for handling exceptions and interrupts
	- MIPS also has a special set of instructions to update the TLB
![[screenshots/Pasted image 20260415220242.png]]

![[screenshots/Pasted image 20260415220304.png]]

