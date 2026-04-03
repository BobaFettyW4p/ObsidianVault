## Introduction

- programmers have always wanted unlimited amounts of fast memory
 	- a computer does not need to access all of its code or data at once with equal probability
  		- otherwise, ti would be impossible to make most memory accesses fast and still have large memory in computers
- the *principle of locality* underlines the way that programs operate
 	- programs access a relatively small portion of their address space at any instant of time
 	- there are two different types of locality:
  		- *temporal locality*
   			- if an item is referenced, it will tend to be referenced again soon
  		- *spatial locality*
    - if an item is referenced, items whose addresses are close by will tend to be referenced soon
![[Pasted image 20260401215208.png]]
- locality in programs arises from natural program structures
 	- most programs contain loops, so instructions and data are likely to be accessed repeatedly
  		- temporal locality
 	- since instructions are normally accessed sequentially, programs also show high spatial locality
- we are able to take advantage of the principle of locality by implementing computer memory as a *memory hierarchy*
 	- consists of multiple levels of memory with different speeds and sizes
  		- faster memories are more expensive per bit than slower ones
   			- therefore smaller as well
 	- the goal is to present the user with as much memory as is avialable in the cheapest technology, while providing access at the speed offered by the fastest memory
  		- data is similarly hierarchical
   			- a level closer to the processor is generally a subset of any level further away
    				- all data is stored at the lowest level
- a memory hierarchy can consist of multiple levels, but data is copied between only two adjacent levels at a time

![[Pasted image 20260402185228.png]]

- the upper level (the one closer to the processor) is smaller and faster than the lower level
 	- the upper level uses technology that is more expensive
- the minimum unit of information that can either be present or not present in a cache is called a *block* or *line*
- if the requested data appears in some block in the upper level, this is called a *hit*
 	- otherwise, it is called a *miss rate*
- the *hit rate* is the fraction of memory accesses found in the upper level
 	- the *miss rate* $(1- \text{hit rate})$ is the fraction of memory accesses not found in the upper level
- the main purpose for having a memory hierarchy is  performance, so the time to service hits and misses matters
 	- *hit time* is the time to access the upper level of the memory hierarchy
  		- includes time needed to determine whether the access is a miss or a hit
 	- the *miss penalty* is the time to replace a block in the upper level with the corresponding block from the lower level, plus the time to deliver this block to the processor
 	- as the upper level is smaller and built with more expensive memory, the hit time will be lower than the time to access next level in the hierarchy
- the concepts used to build memory systems affect many aspects of a computer
 	- how the OS manages memory and I/O
 	- how compilers generate code
 	- how applications use the computer
- as all programs spend much of their time accessing memory, the memory system is a major factor in determining performance

##### The Big Picture

- programs exhibit both temporal locality...
 	- the tendency to reuse recently accessed data items
- ...and spatial locality
 	- the tendency to reference data items close to other recently accessed items
- memory hierarchies take advantages of temporal locality by keeping more recently accessed data items closer to the processor
 	- take advantage of spatial locality by moving blocks consisting of multiple contiguous words in memory to upper levels of the hierarchy
- a memory hierarchy uses smaller and faster memory technologies close to the processor
 	- accesses that hit in the highest level of the hierarchy can be processed quickly
  		- misses go to lower levels, which are larger but lsower
- if the hit rate is high enough, the memory hierarchy has an effective access close to that of the highest level and a size equal to the lowest level
- in most systems, the memory is a true hierarchy
 	- data cannot be present in level $i$ unless it is also present in level $i+1$
![[Pasted image 20260402191617.png]]

## Memory Technologies

- there are four primary technologies used today in memory hierarchies
 	- main memory is implemented from DRAM (dynamic random access memory)
 	- levels closer to the cache use SRAM (static random access memory)
 	- flash memory is the nonvolatile secondary memory in personal mobile devices
 	- magnetic disk is the cheapest and slowest memory technology

![[Pasted image 20260402203228.png]]

### SRAM Technology

- integrated circuits that are memory arrays with a single access port
 	- can provide either a read or a write
- SRAMs have a fixed access time to any data
 	- read and write times may differ
- SRAMS have no need to refresh
 	- access time is very close to cycle time
- SRAMs use 6-8 transistors per bit to prevent information from being disturbed when read
 	- only needs minimal power to retain charge in standby mode
- in the past, most computers used separate SRAM chips for primary, secondary, and tertiary caches
 	- today, caches are integrated onto the processor chip

### DRAM Technology

- in DRAM, the value kept in a cell is stored as a charge in a capacitor
 	- in contrast, SRAM can keep a value indefinitely as long as power is applied
 	- a single transistor is used to access this, either to read or overwrite the charge stored
- DRAMs used a single transistor per bit of storage
 	- much denser and cheaper per bit than SRAM
- As DRAMs store charge on a capacitor, it cannot be kept indefinitely
 	- must periodically be refreshed
 	- this is why it's referred to as dynamic
- to refresh the cell, we read the contents and then write it back
 	- charge can be kept for several milliseconds
- DRAMs use a two-level decoding structure which allows an entire row to refreshed in a single read cycle followed by a write cycle
- row organization helps with refresh, but it also helps with performance
![[Pasted image 20260402205026.png]]
- DRAMs buffer rows for repeated access
 	- acts like a SRAM
  		- random bits can be accessed in the buffer until the next row is accessed
   			- improves access time significantly
   			- also means making the chip wider improves memory bandwidth
- to further improve the interface to processors, some DRAMs added clocks and are properly called Synchronous DRAMs or SDRAMs
 	- use of a clock eliminates the time for the memory and the processor to synchronize
 	- the speed advantage comes from the ability to transfer the bits int he burst without having to specify additional address bits
 	- instead, the clock transfers successive bits in a burst
  		- the fastest version is called Double Data Rate (DDR) SRDRAM
   			- the latest version is called DDR4
    				- DDR-3200 RAM can do 3200 million transfers per second
     					- this means it has a 1600 MHz clock
     					- this requires clever organization inside the DRAM
      						- instead of just a faster row buffer, DRAM can be interanlly organized to read or write from multiple *banks*
       							- each bank has its own row buffer
        								- each bank can read or write simultaneously
![[Pasted image 20260402205512.png]]
- personal devices use individual DRAMs, server memory is often sold on small boards called *dual inline memory modules (DIMMs)*
 	- DIMMs typically contain 4-16 DRAMs, normally organized to be 8 bytes wide

>"One way to measure the performance of the memory system behind the caches is the Stream benchmark. It measures the performance of long vector operations. They have no temporal locality and they access arrays that are larger than the cache of the computer being tested.

### Flash memory

- a type of electrically erasable programmable read-only emmory (EEPROM)
- unlike disks and DRAM, writes can wear out flash memory bits
 	- most flash products include a controller to spread the writes
  		- by remapping blocks that have been written many times
   			- *wear levelling*
    				- lowers the potential performance of flash memory, but prevents it from exceeding the inherent write limits

### Disk Memory

- each disk is divided into concentric circles, called *tracks*
 	- tens of thousands per surface
 	- each track is divided into *sectors* that contain the info
  		- each track may have thousands of sectors, 512 to 4096 bytes in size
- the term *cylinder* is used to refer to all tracks under the heads at a given point on all surface
- in order to access data, the OS must direct the disk through a three step process
 	- first, the head must be positioned over the proper track
  		- called a *seek*, the time to move the head to the desired track is called *seek time*
  		- average seek times are 3 ms to 13 ms, but that's a loose upper bound
 	- next, we must wait for the desired sector to rotate under the read/write head
  		- called *rotational latency* or *rotational delay*
  		- disks rotate at 5400 RPM to 15,000 RPM
  		- the average rotational latency at 5400 RPM is:

$$
\text{Average Rotational Latency} = \frac{\text{0.5 rotation}}{{\text{5400 RPM}}} = \frac{\text{0.5 rotation}}{\text{5400 RPM}/(60 sec/min)} = 0.0056\ seconds = 5.6 ms
$$

- the final component is *transfer time*
 	- the time to transfer a block of bits
  		- function of the sector size, rotation speed, and the recording density of a track
  		- between 100 and 200 MB/sec in 2012
 	- most disc controllers ahve a built in cache that stores sectors as they are passed over
  		- cache transfer rates are typically higher
   			- upt o 750 MB/sec in 2012
- the two primary differences between magnetic disks and semiconductor memory technologies
 	- disks have a slower access time because they are mechanical, tey are cheaper per bit
 	- disks are nonvolatile like flash, but there is no write wear-out problem
  		- flash is much more rugged, however

## The Basics of Caches

- *cache* is the name chosen to represent the level of memory hierarchy between the processor and main memory in the first commercial computer
 	- also used to refer to any storage managed to take advantage of local access
- if each word can go into exactly one place in the cache, then it is straightforward to find the word if it is in the cache
 	- can do this by assign a cache location for each word in memory and then check that location
  		- this is called *directed mapped*
- almost all direct-mapped caches use thsi mapping to find a block:

$$
\text{(Block address) modulo (Number of blocks in the cache)}
$$

- because each cache location can contain the contents of a number of different memory locations, how do we know whether the data in the cache corresponds to a requested work?
 	- we add a set of *tags* to the cache
  		- a field used for a memory hierarchy that contains the address info required to identify whether the associated block in the hierarchy corresponds to a requested word
  		- only needs to contain the upper portion of the address to do this
 	- also need a way to recognize a cache block does not have valid information
  		- the most common method is to add a *valid bit*, which indicates whether an entry contains a valid address
   			- if it's not set, it doesn't
![[Pasted image 20260402212101.png]]

![[Pasted image 20260402212113.png]]

- in general, handling reads is easier for a cache than handling writes
 	- reads do not change the contents of the cache

>"Caching is perhaps the most important example of prediction. It relies on the principle of locality to try and find desired data in the higher levels of the memory hierarchy, and provides mechanisms to ensure when the prediction is wrong it finds and uses the proper data from the lower levels. In modern computers, hit rates are often higher than 95%

#### Accessing a cache

- given this cache
![[Pasted image 20260402212433.png]]
- here is how some scenarios would be handling trying to find things in it
![[Pasted image 20260402212502.png]]
- a referenced address can be divided into:
 	- a *tag field*, which is used to compare with the value of the tag field fo the cache
 	- a *cache index*, which is used to select the block
- the index of a cache block, along with the tag contents of that block uniquely specify the memory address of the word contained in the cache block
- because the index field is used to reference the cache, and because an $n$-bit field has $2^n$ values, the total number of entries in a direct mapped cache must be a power of 2
- in MIPS architecture, words are aligned to multiples of 4 bytes
 	- this means the least significant two bits of every address specify a byte within a word
  		- the least significant two bits are ignored when selecting a word in the block
- the total number of bits needed for a cache is a is a function of the cache size and address size
 	- the cache includes both the storage for the data and the tags
 	- consider the following situation:
  		- 32 bit address
  		- a direct mapped cache\
  		- cache size is $2^n$ blocks
   			- $n$ bits are used for the index
  		- the block size is $2^m$ words ($2^{m+2}$ bytes)
   			- $m$ bits are used for the word within the block, and two bits are used for the byte part of the address
  		- in this example, the size of the tag field is $32 - (n+m+2)$
  		- the total number of bits in a direct-mapped cache is $2^n * (\text{block size} + \text{tag size} + \text{valid field size})$
  		- since the block size is $2^m$ words ($2^{m+5}$ bits), and we need 1 bit for the valid field, the number of bits in this cache is as follows:

$$
2^n * (2^m * 32 (32 -n -m -2) + 1) = 2^n * (2^m * 32 +  31 - n - m)
$$

- this is the actual size in bits, but the naming convention is to exclude the size of the tag and valid field and only count the size of the data
 	- the cache in the below image is referrred to as a 4 KiB cache
![[Pasted image 20260403112500.png]]

## Questions
>
>How many total bits are required for a direct-mapped cache with 16 KiB of data and 4-word blocks, assuming a 32-bit address

- we know that 16 KiB is 4096 ($2^{12}$ words)
- with a block size of 4($2^2$) words, there are 1024 ($2^{10}$) blocks
 	- each block has $4 * 32$ or 128 bits of data plus a tag which is $32 - 10 - 2 - 2$ bits as well as a valid bit
- this means the total cache size is:

$$
2^{10} * (4 * 32 + (32 - 10 -2 -2) + 1) = 2^{10} * 147 = 147 \text{ Kibibits}
$$
>Consider a cache with 64 blocks and a block size of 16 bytes. To what block number does byte address 1200 map?

- the block is given by $\text{(block address) modulo (number of blocks in the cache)}$
- where the address of the block is:

$$
\frac{\text{Byte address}}{\text{Bytes per block}}
$$

- this block address is the block containing all addresses between:

$$
\left| \frac{\text{Byte address}}{\text{Bytes per block}} \right| * \text{Bytes per block}
$$
and
$$
\left| \frac{\text{Byte address}}{\text{Bytes per block}} \right| * \text{Bytes per block} + (\text{Bytes per block -1})
$$

- with 16 bytes per block, byte address 1200 corresponds to block address:

$$
\left| \frac{1200}{6} \right|= 75
$$

- larger blocks exploit spatial locality to lower miss rates
- increasing block size usually decreases the miss rate
 	- it may go up eventually if the block size becomes a significant fraction of the cache size
  		- there are a small number of blocks, with great competition for them
   			- blocks become bumped out of the cache before many of its words are accessed
- spatial locality among words in the block decreases with a very large block
 	- the benefits in miss rate decrease
![[Pasted image 20260403114534.png]]

- a serious problem associated with just increasing the block size is the cost of a miss increases
 	- the miss penalty is determined by the time required to fetch the block from the lower level of the hierarchy and load it into the cache
  		- the time to fetch the block has two parts:
   			- the latency to the first word
   			- transfer time for the rest of the block
  		- as the transfer time increases along with an increased block size, so will the miss penalty

### Handling Cache Misses

- a *cache miss* is a request for data from the cache that cannot be filled because the data is not present in the cache
- the control unit must detect a miss, and then process that miss by fetching the data from memory (or from a lower level cache)
 	- if the cache reports a hit, the computer continues using the data as if nothing happened
- modifying control of a processor to handle a hit is trivial
 	- misses require some work
- cache miss handling is done in collaboration with the processor control unit and a separate controller that initiates the memory access and refills the cache
- processing the cache miss creates a pipeline stall as opposed to an interrupt
 	- an interrupt would require saving the state of all registers
- cache misses can stall the entire processor, which freezes the contents of the temporary and programmer-visible registers
 	- this is done while we wait for memory
- more sophisticated out-of-order processors exist and can allow execution of instructions while waiting for a cache miss
 	- our examples assume in-order processors that stall on cache misses
- how are instruction misses handled?
 	- same approach is easily extended to data misses
 	- if an instruction access results in a miss, then the content of the Instruction register is invalid
 	- to get the proper instruction into the cache, we must be able to instruct the lower level in the memory hierarchy to perform a read
 	- since the program counter is incremented in the first clock cycle of execution, the address of the instruction that generates an instruction cache miss is equal to the value of program counter minus 4
 	- once we have the address, we need to instruct the main memory to perform a read
 	- we wait for the memory to respond (this can take several clock cycles), and then write the words containing the desired instruction into the cache
- total steps taken on an instruction cache miss:
- Send the original PC value ($\text{current PC} - 4$) to memory
- Instruct main memory to perform a read and wait for the memory complete access
- Write the cache entry, putting data from memory in the data portion of the entry
- restart instruction execution at the first step, which will refetch the instruction
- this instruction is now located in the cache
- control of cache on a data access is identical; on a miss, we simply stall the processor until the memory responds with the data

## Handling Writes

- suppose on a store instruction, we wrote the data into the data cache without changing main memory
	- after the write into the cache, memory would have a different value from the cache
	- in this cache, the cache and memory are considered to be *inconsistent*
- the best way to prevent this is to always write data into both the memory and the cache
    - this scheme is referred to as *write-through*
- another key aspect of writes is what occurs on a write miss
	- we fetch the words of the block from memory
	- once the block is fetched and placed into the cache, we can overwrite the wrod that caused the miss into the cache block
	- we write the word to main memory using the full address of the word
	- this design is very simple to handle writes, but it is not very performant
- a write-through scheme requires every write to be written to main memory, which is slow (at least 100 clock cycles)
	- suppose 10% of total instructions were stores
	- If CPI without cache misses was 1.0, spending 100 extra cycles on every write yields a true CPI of $1.0 + 100 * .10 = 11$
		- e.g. performance is reduced by a factor of 10
- one possible solution is to use a *write buffer*
  - a write buffer stores data while it is waiting to be written to memory
    - once data has been written to the write buffer, the processor can continue calculating other data without waiting
      - once the data in the write buffer is written to memory, the entry in the write buffer is freed and the write buffer is ready to accept a new value into the write buffer
- if the write buffer is full when the processor reaches a write, the processor must stall until the write buffer is ready to receive the write
	- if the rate at which the memory can complete writes is less than the rate at which the processor is generating writes, no amount of buffering will help
- the alternative to a write-through scheme is referred to as *write-back*
	- when a write occurs under a write-back scheme, the new value is only written to the block in the cache
		- the modified block is written to the next lowest level in the hierarchy when it's replaced in the cache
	- write back schemes can improve performance especially when processors can generate writes as fast or faster than writes can be handled by main memory
		- write back schemes are more complex to implement than write-through schemes
- writes introduce several complications into caches that are not present for reads
	- policy on write misses
		- most common policy is to allocate a block in the cache
			- called *write allocate*
			- this lock is fetched from memory and the appropriate portion of the block is overwritten
		- an alternative is to update the portion of the block in memory but not in the cache
			- *no write allocate*
			- sometimes programs write entire blocks of memory
				- the fetch associated with the initial write may not be necessary
	- efficient implementation of writes in write-back caches
		- more difficult in a write-back compared to a write-through cache
			- write through can write the data into the cache and read the tag
				- if there's a mismatch, then a cache miss occurs
				- in a write-through cache, overwriting the block isn't catastrophic, as the correct value is in memory
				- in a write-back cache, the block must be written back to memory before the cache value is updated
					- otherwise, the contents of the block are destroyed in the cache and not backed up in any lower levels of the hierarchy
					- since we can't overwrite the block, stores either require two cycles (a cycle to check for a hit followed by a cycle to perform the write), or a write buffer to store the data
						- write-through caches can always be done in one cycle
### An example cache: the Intrinsity FastMATH processor
- embedded microprocessor that uses the MIPS architecture
- 12 stage pipeline
	- at peak speed, thee processor can request an instruction and a data word on every clock
		- separate instruction and data caches are used to accomplish this without stalling
		- each cache is 16 KiB or 4096 words with 16 word blocks
- read requests are straightforward
	- since there are separate data and instruction caches, there must be separate control signals to read and write each cache
- the steps for a read are as follows:
	- send the address to the appropriate cache
	- if the cache signals hit, the requested word is available
		- since there are 16 words, we must select the correct one
			- a block index field select the requested word from the 16 words in the block
	- if the cache signals miss, we send the address to main memory
		- when the memory returns with the data, we write it into the cache and read it
- FastMATH offers both write-through and write-back for writes
	- the OS decides which one to use for an application
![[Pasted image 20260403133329.png]]

![[Pasted image 20260403133411.png]]
- *split cache*
	- a scheme in which a level of the memory hierarchy is comprised of two independent caches that operate in parallel with each other; one handles instructions and the other handles data
- a combined cache with a total size equal to the sum of two split caches will usually have a better hit rate
	- the combined cache doesn't divide the number of entries that may be used by instructions from those that use data
	- however, nearly all processors use split instruction and data caches to increase cache bandwidth and match modern data pipelines
## Measuring and Improving Cache Performance
- CPU time can be divided into clock cycles that the CPU spends executing program and clock cycles the CPU spends waiting for the memory system. $\therefore,$
$$
\text{CPU time} = (\text{CPU execution clock cycles} + \text{Memory-stall clock cycles}) * \text{Clock cycle time}
$$
- the memory-stall clock cycles come primarily from cache misses
$$
\text{Memory-stall clock cycles} = (\text{Read-stall cycles} + \text{Write-stall cycles})
$$
- writes are more complication
	- for a write-through scheme, we have two sources of stalls
		- write misses
			- require us to fetch the block before continuing the write
		- write buffer stalls
			- occur when the write buffer is full when a write occurs
		- $\therefore,$
$$
\text{Write-stall cycles} = (\frac{\text{Writes}}{\text{Program}} * \text{Write miss rate} * \text{Write miss penalty}) + \text{Write buffer stalls}
$$
-  because write buffer stalls depend on the proximity of writes (and not just the frequency), it's not possible to give a simple equation to calculate them
	- in systems with a reasonable write buffer depth and a memory capable of accepting writes that significantly exceeds the average write frequency of programs, the write buffer stalls will be small
		- can be safely ignored
- write back schemes have potential additional arising from the need to write a cache block back to memory when the block is replaced
- in most write-through cache organizations, the read and write miss penalties are the same
	- the time to fetch the block from memory
- if we assume buffer stalls are negligible, we can combine reads and writes with a single miss rate and miss penalty
$$
\text{Memory-stall clock cycles} = \frac{\text{Memory accesses}}{\text{Program}} * \text{Miss rate} * \text{Miss penalty}
$$
- this can also be expressed as:
$$
\text{Memory-stall clock cycles} = \frac{\text{Instructions}}{\text{Program}} * \frac{\text{Misses}}{\text{Instruction}} * \text{Miss Penalty}
$$
#### Calculating Cache Performance
> Assume the miss rate of an instruction cache is 2% and the miss rate of the data cache is 4%. If a processor has a CPI of 2 without any memory stalls and the miss penalty is 100 cycles for all misses, determine how much faster a processor would run with a perfect cache that never missed. Assume the frequency of all loads and stores is 36%

- the number of miss cycles for instructions in terms of the instruction count $I$ is:
$$
\text{Instruction miss cycles} = I * .02 * 100 = 2.00 I
$$
- as the frequency of loads and stores is 36%, we can find the number of memory miss cycles for data references:
$$
\text{Data miss cycles} = I*.36*.04*100 = 1.44I
$$
- $\therefore,$ the total number of memory-stall cycles is $2.00I + 1.44I = 3.44I$
	- thus, the total CPI including memory stalls is $2.00 + 3.44 = 5.44$
- since this change in CPI is not accompanied by any change in instruction count or clock rate, the ratio of CPU execution times is
$$
\frac{\text{CPU time with stalls}}{\text{CPU time with perfect cache}} = \frac{I * CPI_{stall} * \text{Clock cycle}}{I * CPI_{perfect} * \text{Clock cycle}} = \frac{CPI_{stall}}{CPI_{perfect}} = \frac{5.44}{2} = 2.72
$$
- $\therefore$, the performance with the perfect cache is better by 2.72
- what if the processor is made faster, but the memory system is not?
	- the amount of time spend on memory stalls will take up an increasing fraction of execution time
		- **Amdahl's Law**
- suppose we sped up the computer by reducing it's CPI from 2 to 1 without changing the clock rate
	- the system with cache misses would have a CPI of $1+3.44 = 4.44$
		- the system with the perfect cache would be $\frac{4.44}{1} = 4.44$ times as fast
		- the amount of execution time spent on memory stalls would rise from $\frac{3.44}{5.44} = 63\%$ to $\frac{3.44}{4.44} = 77\%$ 
			- similarly, increasing clock rate without changing the memory system also increases performance lost due to cache misses
- these examples assume hit time is not a factor in determining cache performance
	- if hit time increases, the total time to access a word from the memory system will increase
		- which can lead to an increase in processor cycle time
		- one way this can happen is by increasing cache size
- to capture this, designers sometimes use *average memory access time (AMAT)* as  away to examine alternative cache designs
	- AMAT is the average time to access memory considering both hits and misses and the frequency of different accesses
$$
\text{AMAT} = \text{Time for a hit} + \text{Miss rate} * \text{Miss penalty}
$$
#### AMAT Example
>Find the AMAT for a processor with a 1 ns clock cycle time, a miss penalty of 20 clock cycles, a miss rate of 0.05 misses per instruction, and a cache access time (including hit detection) of 1 clock cycle. Assume the read and write miss penalties are the same and include other write stalls

- the average memory access time per instruction is:
$$
\text{AMAT} = \text{Time for a hit} + \text{Miss rate} + \text{Miss penalty}
$$
$$
= 1 + 0.05 * 20*
$$
$$
=2 \text{ clock cycles}
$$
or  2 ns

#### Reducing Cache Misses by More Flexible Placement of Blocks
- so far, when we place a block in the cache, we have used a simple placement scheme
	- a block can go in exactly one place in the cache
	- this is called *direct-mapped* as there is a direct mapping from any block address in memory to a single location in the upper level of the hierarchy
- however, there are actually a range of schemes for placing blocks
	- direct mapped is only one extreme
	- the other extreme is a scheme where a block can be placed in any location in the cache
		- called *fully associative*
			- a block in memory may be associated with any entry in the cache
			- to find a block in a fully associative cache, all entries must be searched
				- this is made practical by doing it in parallel with a comparator associated with each cache entry
					- comparators significantly increase hardware cost, and so these caches are only cost-efficient for caches with small numbers of blocks
	- the middle range is called *set associative*
		- in a set-associative cache, there are a fixed number of locations where blocks can be placed
			- a set-associative cache with $n$ locations for a block is called an $n$-way set associative cache
				- consists of a number of sets, each with $n$ blocks
					- each block maps to a unique set in the cache, and a block can be placed in any element of that set
					- thus, a set-associative cache combines direct-mapped and fully-associative placement
						- a block is placed into a set and then all blocks in the set are searched for a match
![[Pasted image 20260403141914.png]]

- in a direct mapped cache, the position of a memory block is given by:
$$
(\text{Block number})\ \%\ (\text{Number of blocks in the cache})
$$
- in a set-associative cache, the set containing a memory block is given by:
$$
(\text{Block number})\ \%\ (\text{Number of sets in the cache})
$$
- we can consider all block placement strategies as a variation on set associativity
![[Pasted image 20260403142144.png]]

##### Misses and Associativity in Caches
>Assume there are three small caches, each consisting of four one-word blocks. One cache is fully associative, a second is two-way set asssociative, and the third is direct mapped. Find the number of misses for each cache organization given the following sequence of block addresses: 0, 8, 0, 6, and 8

- the direct mapped case is easiest
	- let's determine where each block address will be mapped in the cache
![[Pasted image 20260403142500.png]]
- we can fill in the cache contents after each reference
	- we will use a blank entry to mean the block is invalid, colored text will show a new entry added to the cache for the associated reference, and plain text to show an old entry in the cache
![[Pasted image 20260403142546.png]]
- the direct mapped cache generates 5 misses for the 5 accesses
- the set-associative cache has two sets (with indices 1 and 0) with two elements per set.
	- first we determine where each block address maps
![[Pasted image 20260403142830.png]]
- because we have a choice of which entry in a set to replace on a miss, we need a replacement rule
	- set-associative caches generally replace the least recently used block within a set
		- the block that was used furthest in the past is replaced
		- under this rule, the contents of the cache after each reference will look like this:
![[Pasted image 20260403142937.png]]
- note: when block 6 is referenced, it replaces block 8 since block 8 has been least recently referenced than block 0
- the two-way set-associative cache has 4 misses, one less than the direct mapped cache
- the fully associative cache has 4 cache blocks in a single set
	- any memory block can be stored in any cache block
	- the fully associative cache has the best performance, with three total misses, as so:
![[Pasted image 20260403143059.png]]

- for this series of references, three misses is optimal behavior, as three unique block addresses are accessed
	- if we had 8 blocks in the cache, there would be no replacements for the two-way set-associative cache, and it would have the same number of misses as the fully associative cache
- how much of a reduction in miss rate is achieved by associativity?
![[Pasted image 20260403143228.png]]

### Locating a Block in the Cache
- consider a set associative cache
	- similar to a direct-mapped cache, each block includes an address tag that gives the block address
		- the tag of every block within the appropriate set is checked to see if it matches the block address from the processor
		- the index value is used to select the set containing the address
			- once selected, every tag within the set must be searched
				- they are searched in parallel as sequential search would be too slow
![[Pasted image 20260403143345.png]]
- if total cache size is kept constant, increasing associativity increases the number of blocks per set
	- the numer of simultaneous compares needed to perform the search in parallel
		- each increase in associativity by a factor of 2 doubles the number of blocks per set
			- halves the number of sets
			- decreases the size of the index by 1 bit and increases the size of the tag by 1 bit
		- a fully associative cache effectively has one set and all blocks must be checked in parallel
			- thus, there is no index, and the entire addresses is compared against the tag of every block
				- we search the entire cache without indexing
- in a direct-mapped cache, only a single comparator is needed
	- the entry can only be in one block and we access the cache by indexing
	- similarly, in a four-way set-associative cache, four comparators are needed
		- along with a 4-to-1 multiplexor to choose among the four potential members of the selected set
		- cache access consists of indexing the appropriate set and then searching the tags of the set
		- the costs of an associative cache are extra comparators and any delay imposed by having to do the compare and select from among elements of the set
![[Pasted image 20260403143933.png]]
- choosing between direct-mapped, set-associative or fully associative mapping in a memory hierarchy depends on the costs of a miss versus the cost of implementing associativity
	- both in time and extra hardware
- *Content Addressable Memory(CAM)* is a circuit that combines comparison and storage in a single device
	- instead of supplying an address an dreading a word like RAM, you supply the data and the CAM sees if it has a copy and returns the index of the matching row
		- CAMs let cache designers afford to implement higher set associativity than if they needed to build the hardware out of SRAMs and comparators

#### Choosing Which Block to Replace
- when a miss occurs in a direct-mapped cache, the requested block can go in exactly one position
	- the data occupying that block must be replaced
		- in an associative cache, we have a choice of where to place the requested block
			- thus, which block will be replaced
		- in a fully associative cache, every block is a candidate for replacement
- the most commonly used scheme is *least recently used (LRU)*
	- the block replaced is the one that has been unused for the longest time
	- LRU replacement is implemented by keeping track of when each element in a set was used relative to the other elements in the set
		- for a two-way set-associative cache, tracking when the two elements were used can be implemented by keeping a single bit in each set and setting the bit to indicate an element whenever that element is referenced
			- as associativity increases, implementing LRU gets harder

##### Size of Tags versus Set Associativity
>Increasing associativity requires more comparators and more tag bits per cache block. Assuming a cache of 4096 blocks, a 4-word block size, and a 32-bit address, find the total number of sets and the total number of tag bits for caches that are direct mapped, two-way and four-way set associative, and fully associative

- there are 16 ($2^4$) bytes per block
	- a 32-bit address yields $32-4 = 28$ bits to be used for index and tag
	- the direct mapped cache has the same number of sets as blocks
		- 12 bits of index
			- this is because $log_{2}4096 = 12$
		- thus, the total number is $(28-12)*4096 = 16*4096 = 66K$ tag bits
- each degree of associativity decreases the number of sets by a factor of 2 and thus decreases the number of bits used to index the cache by 1 and increases the number of bits in the tag by 1
	- for a two-way set-associative cache, there are 2048 sets
		- the total number of tag bits is $(28-11) * 2 * 2048 = 34 * 2048 = 70K$ tag bits
	- for a four-way set-associative cache, there are 1-24
		- the total number of tag bits is $(28-10)*4*1024 = 72*1024 = 74K$ tag bits
	- for a fully associative cache, there is one set with 4096 blocks, and the tag is 28 bits, leading to $28*4096*1 = 115K$ tag bits
### Reducing the Miss Penalty Using Multilevel Caches
- all modern computers make use of caches
- to close the gap between fast clock rates and increasing long times required to access DRAM, most microprocessors support an additional level of caching
	- this second level cache is normally on the same chip and is accessed whenever a miss occurs in the primary cache
		- if the second level cache contains the desired data, the miss penalty for the first-level cache will essentially be the access time of the second level cache
			- this is much less than the time to access main memory
			- if the second level cache does not contain the data either, main memory access is needed, which incurs a greater miss penalty
##### Performance of Multilevel Cachces
>Suppose we have a processor with a base CPI of 1.0, assuming all references hit in the primary cache, and a clock rate of 4.0 GHz. Assume a main memory access time of 100 ns, including all miss handling. Suppose the miss rate per instruction of the primary cache is 2%. How much faster will the processor be if we add a secondary cache that has a 5 ns access time for either a hit or a miss and is large enough to reduce the miss rate to main memory to 0.5%?

- the miss penalty to main memory is
$$
\frac{100\ ns}{0.25 \frac{ns}{\text{clock cycle}}} = 400 \text{ clock cycles}
$$
- the effective CPI with one level of caching is given by
$$
\text{Total CPI} = \text{Base CPI} + \text{Memory-stall cycles per instruction}
$$
- for the processor with one level of caching:
$$
\text{Total CPI} = 1.0 + \text{Memory-stall cycles per instruction} = 1.0 + 2\% * 400 = 0
$$
- with two levels of caching, a miss in the primary (or first level cache) can be satisfied either by the secondary cache or by main memory. The miss penalty for access to the second-level cache is:
$$
\frac{5\ ns}{0.25\frac{ns}{\text{clock cycle}}} = 20 \text{ clock cycles}
$$
- if the miss is satisfied in the secondary cache, this is the entire miss penalty
	- if the miss needs to go to main memory, then the total miss penalty is the sum of the secondary cache access time and the main memory access time
- for a two level cache, total CPI is the sum of the stall cycles from both levels of cache and the base CPI:
$$
\text{Total CPI} = 1 + \text{Primary stalls per instruction} + \text{Secondary stalls per instruction} = 1 + 2\% * 20 + 0.5\% * 400 = 1 + 0.4 + 2.0 = 3.4
$$
- $\therefore$, the processor with the secondary cache is faster by
$$
\frac{9.0}{3.4} = 2.6
$$
- alternatively, you can compute the stall cycles by summing the stall cycles of references that hit in the secondary cache (($2\% - 0.5\%$)* 20 = 0.3)
	- those references go to main memory, which must include the cost to access the secondary cache as well as the main memory access time, which is ($0.5\% * (20 + 400) = 2.1$)
		- the sum of these two values, $1.0 + 0.3 + 2.1$ equals the value we received earlier ($3.4$)
- design considerations for a primary and secondary cache are significantly different
	- the presence of the other cache changes the best choice compared to a single-level cache
		- a two-level cache structure allows the primary cache to focus on minimizing hit time to yield a shorter clock cycle or fewer pipeline stages
			- the secondary cache can focus on miss rate to reduce the penalty of long memory access times
	- these effects can e seen by comparing each stage to the optimal design of a single level of cache
		- in comparison, the primary cache of a *multilevel cache* is smaller
			- the primary cache will use a smaller block size to reduce the miss penalty
			- the secondary cache will be much larger
				- the access time of the secondary cache is less critical
				- a larger total size corresponds to a larger block size than is appropriate with a single level cache
				- will often use higher associativity
					- the focus is on reducing miss rates
![[Pasted image 20260403154422.png]]

### Software Optimization via Blocking
- many software optimizations were invented that can dramatically improve performance by reusing data within the cache and lower miss rates with improved temporal locality
- when dealing with arrays, we can get good performance if we store the entire array such that accesses to the array are sequential in memory
	- consider a situation where we are dealing with multiple arrays with some accessed by rows and some by columns
		- storing them row-by-row (*row major order*) or column-by-column (*column major order*) does not solve the problem because both rows and columns are used in every major iteration
			- instead of operating on entire rows or columns, *blocked* algorithms operate on submatrices (*blocks*)
				- the goal is to maximize accesses to the data loaded into the cache before the data is replaced
					- i.e. improve temporal locality to reduce cache misses
					- consider this block of code:
```
for(int j=0; j<n; j++)
{
double cij = C[i+j*n]; /* cij = C[i][j] */
for (int k=0; k<n; k++)
cij += A[i+k*n] * B[k+j*n]; /* cij = A[i][k] * B[k][j] */
C[i+j*n] = cij; /* C[i][j] = cij */
}
}
```
- it reads all $N$ by $N$ elements of $B$, reads the same $N$ elements that correspond to one row of $A$ repeatedly and writes what corresponds to one row of $N$ elements of $C$
![[Pasted image 20260403155204.png]]
- this gives us a snapshot of the three arrays
	- a dark shade is a recent access, a light shade is an older access, and white means not yet accessed
- the number of capacity misses depends on $N$ and the size of the cache
	- if it can hold all three $N$-by-$N$ matrices, then all is well provied there is no cache conflicts
	- if the cache can hold one $N$-by$N$ matrix and one row of $N$, then at least the $i$th row of $A$ and the array $B$ may stay in the cache
		- less than that and misses may occur for both $B$ and $C$
			- in the worst case, there would $2 N^3 + N^2$ memory words accessed for $N^3$ operations
- to ensure that the elements being accessed can fit in the cache, the original code is changed to compute on a submatrix
	- we invoke the same code on matrices of size $BLOCKSIZE$ by $BLOCKSIZE$
		- $BLOCKSIZE$ is what is called the *blocking factor*
- the blocked version of the code looks like so:
```
#define BLOCKSIZE 32
void do_block (int n, int si, int sj, int sk, double *A, double *B, double *C)
{
	for (int i=si; i<si+BLOCKSIZE; ++i)
	{
		for (int j = sj; j < sj+BLOCKSIZE; ++j)
		{
		double cij = C[i+j*n]; /* cij = C[i][j] */
		for (int k = sk; k < sk+BLOCKSIZE; k++)
			{
			cij += A[i+k*n] * B[k+j*n] /* cij+=A[i][k]*B[k][j] */
			C[i+j*n] = cij; /* C[i][j] = cij */
			}
		}
	}	
}
void dgemm (int n, double* A, double* B, double* C)
{
	for (int sj=0; sj < n; sj += BLOCKSIZE)
	{
		for (int si = 0; si < n; si += BLOCKSIZE)
		{
			for (int sk=0; sk < n; sk+= BLOCKSIZE)
			{
				do_block(n,si,sj,sk,A,B,C)
			}
		}
	}
}
```

- access age to the array is as follows:
![[Pasted image 20260403160210.png]]
- as we can see, there are fewer accesses to the arrays overall
- while blocking is intended to reduce cache misses, it can also be used to help register allocation
	- by taking a small blocking size such that the block can be held in registers, we can minimize the number of loads and stores in the program, which improves performance
![[Pasted image 20260403160321.png]]
- multilevel caches create several complications
	- there are now several different types of misses, each with its own corresponding miss rate
		- *global miss rate* - the fraction of references that miss in all levels of a multilevel cache
		- *local miss rate* - the fraction of references to one level of a cache that miss
		- because the primary cache filters accesses, the local miss rate is generally much higher than the global miss rate for all level in a multilevel cache hierarchy
			- global miss rates dictates how often main memory must be accessed
		- there is no general way to calculate overlapped miss latency
## Dependable Memory Hierarchy
- memory hierarchy doesn't forget
	- fast but undependable isn't attractive
### Defining Failure
- assume you have a specification of proper service
	- thus, we can see a system alternating between two states of delivered service
		- *service accomplishment*, where the service is delivered as expected
		- *service interruption*, where the delivered service is different form the specified service
			- transitions from state 1 to state 2 are caused by *failures*, and transitions from state 2 to state 1 are called *restorations*
				- failures can be permanent or intermittent
				- leads to two related terms: reliability and availability
					- *reliaibility* is a measure of the continuous service accomplishment from a reference point
						- *mean time to failure (MTTF)* is a reference point
							- a related term is *annual failure rate (AFR)*
								- the percentage of devices that would be expected to fail in a year for a given MTTF
##### MTTF v. AFR of disks
>Some disks today are quoted to have a 1,000,000 hour MTTF. As 1,000,000 hours is $1,000,000/(365 * 24) = 114$ years, it would seem they practically never fail. Warehouse scale computers that run Internet services such as search might have 50,000 servers. Assume each server has 2 disks. Use AFR to calculate how many disks we would expect to fail each year.

- one year is $365*24 = 8760$ hours
	- a 1,000,000-hour MTTF means an AFR of $8760/1,000,000 = 0.876\%$
		- with 100,000 disks, we expect 876 disks to fail per year
			- i.e. more than 2 per day on average
- service interruption is measured as *mean time to repair (MTTR)*
- *mean time between failures (MTBF)* is simply the sum of MTTF + MTTR
	- while the term MTBF is more widely used, MTTR is often more appropriate
- *availability* is a measure of service accomplishment with respect to the alternation between two states of accomplishment and interruption
- availability is quantified as:
$$
\text{Availability} = \frac{\text{MTTF}}{(\text{MTTF} + \text{MTTR})}
$$
- reliability and availability are actually quantifiable measure, not just synonyms for dependaility
	- shrinking MTTR can help availability as much as increasing MTTF
- availability should be very high
	- often quoted in 'number of nines' of availability
		- one 9: 90% = 36.5 days of repair/year
		- two 9s: 99% = 3.65 days of repair/year
		- three 9s: 99.9% = 526 minutes of repair/year
		- four 9s: 99.99% = 52.6 minutes of repair/year
		- five 9s: 99.999% = 5.26 minutes of repair/year
- to increase MTTF, you can improve quality of components or design systems to continue operations in the presence of failed components
	- failure must be defined in context
		- the failure of a component may not lead to failure of a system
- three ways to increase MTTS:
	- *fault avoidance*: preventing fault occurrence by construction
	- *fault tolerance*: using redundancy to allow the service to comply with the service specification despite faults occurring
	- *fault forecasting*: predicting the presence and creation of faults, allowing the component to be replaced before it fails
#### The Hamming Single Error Correcting, Double Error Detecting Code (SEC/DED)
- to invent redundant codes, it is improtant to talk about how "close" correct bit patterns can be
	- the *Hamming distance* is the minimum number of bits that are different between any two correct bit patterns
		- the distance between `011011` and `001111` is two
		- if the minimum distance between correct patterns is two and we get a one bit error, a correct pattern will turn into an incorrect one
- Hamming used a *parity code* for error detection
	- in a parity code, the number of $1s$ in a word is counted
		- the word has odd parity if there is an odd number of 1s, and even otherwise
	- when a word is written into memory, the parity bit is also written (1 for odd, 0 for even)
		- if the parity of the word and the stored parity bit don't match, an error has occurred
>Calculate the parity of a byte with the value $31_{ten}$ and show the pattern stored to memory. Assume the parity bit is on the right. Suppose the most significant bit was inverted in memory, and then you read it back. Did you detect the error? What happens if the two most significant bits are inverted?

- $31_{ten}$ is $00011111_{two}$ which has 5 1s, and thus has an odd parity
	- to make it even, we need to write a 1 in the parity bit, giving us $000111111_{two}$
		- if the most significant bit is inverted, we would see $100111111_{two}$, which has seven ones
			- since we expect even parity and got odd parity, this would signal an error
			- if two bits were inverted, we would still have even parity and will not detect an error
- Hamming also wanted to resolve errors, not just detect them
	- we use extra parity bits to allow the position identification of a single error
		- Start numbering bits from 1 on the left, as opposed to the numbering of the rightmost bit being 0
		- mark all bit positions that are powers of two as parity bits (positions 1,2,4,8,16, etc.)
		- all other bit positions are used for data bits
		- the position of parity bit determines sequence of data bits that it checks
			- bit 1 checks bits (1,3,5,7,9,...) which are bits where the rightmost bit of address is 1
			- bit 2 checks bits (2,3,6,7,10,...) which are bits where the second bit to the right of the address is 1
			- bit 4 checks bits (4-7, 12-15, 20-23) which are bits where the third bit to the right in the address in 1, and so on
				- each data bit is checked by two or more parity bits
		- finally, we set parity bits to create even for each group
			- this will let us determine which bits are correct and incorrect by using the parity bits
![[Pasted image 20260403163334.png]]
- this does not just apply to single-bit errors
	- for the cost of one bit, we can also detect double bit errors with a minimum Hamming distance of 4
- Single Error Correcting/Double Error Detecting (SEC/DED) is common in memory for servers today
	- 8 byte blocks can get SEC/DED with just one additional byte
		- why most DIMMs are 72 bits wide
- to calculate the number of bits needed for SEC/DED
	- let $p$ be the number of parity bits, and $d$ be the number of data bits
	- if $p$ error correction bits are to point to error bit ($p+d$ cases) plus one case to indicate that no error exists, we need:
$$
2^p \geq p + d + 1 \text{bits}
$$
- therefore:
$$
p \geq log(p+d+1)
$$
- for 8 bits of data, $d=8$
$$
2^p \geq p + 8 + 1
$$
so $p=4
- $p=5$ for 16 bits of data
- $p=6$ for 32 bits of data
- $p=7$ for 64 bits
- and so on