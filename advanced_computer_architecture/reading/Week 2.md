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
