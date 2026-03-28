## Chapter 1: Computer Abstractions and Technology
### Classes of Computing Applications and Their Characteristics
- although a common set of hardware technologies is used in all computers, different applications have different design requirements, and employ these hardware technologies in different ways
- 3 broad categories for computers
	- *Personal Computers (PCs)*  were designed for used by an individual, incorporating a graphics display, a keyboard, and a mouse
		- emphasize delivery of good performance to single users at low cost and execute 3rd party software
	- *Servers* are used for running larger programs for multiple users simultaneously, and are generally accessed only via network 
		- the modern form of what were once much larger computers
		- oriented to carry large workloads, which may cosnsiste of a single complex application (such as a scientific or engineering application), or many small jobs, as would occur in a large web server
		- generally based on software from another source, but are often modified or customized for a particular function
		- built from the same basic technology as desktop computers, but provide for greater computing, storage, and I/O capacity
		- place a greater emphasis on dependability
	- *supercomputers* are a class of computers with the highest performance and cost; they are configured as servers and typically cost tens to hundreds of millions of dollars
	- an *embedded computer* is a computer running inside another device designed for running one predetermined application or collection of software
		- largest class of computers, and span the widest range of applications and performance
			- microprocessors in your car, computers in a TC, the network of processors that run a plane or cargo ship, etc.
		- often have unique application requirements that combine a minimum performance with limitations on cost or power

![[Pasted image 20260325204635.png]]

#### Welcome to the PostPC Era
- the PC is gradually being replaced by the *personal mobile device (PMD)*
![[Pasted image 20260326202423.png]]
- a *personal mobile device* is a small wireless device that connects to the internet
	- they rely on batteries for power and software is installed by downloading apps
	- e.g. smart phones and tablets
- traditional servers are increasingly being replaced by *cloud computing*
	- *cloud computing* refers to large collections of servers that provide services over the internet; some providers rent dynamically varying numbers of servers as a utility
	- these rely upon giant datacenters known as *Warehouse Scale Computers (WSCs)*
		- WSCs contain thousands of servers, and let companies rent portions of them so they can server software
- *Software as a Service* delivers software and data over the internet, generally via a thin program such as a browser that runs on local client devices

- successful programmers have always been concerned about the performance of their programs
	- getting results quickly is vital
	- in the 60s and 70s, the primary constraint was memory size
		- minimize memory space to make programs fast
	- recently, advances in design and memory technology have greatly reduced the importance of small memory size in most applications
	- modern programmers must understand the issues that have replaced the simple memory model of the 1960s
		- parallel nature of processors
		- hierarchical nature of memory
		- energy efficiency

## Eight Great Ideas in Computer Architecture

##### Design for Moore's Law
- *Moore's Law* states that integrated circuit resources double every 18-24 months
- computer architects must anticipate where technology will be when a design finishes rather than reflect current trends
##### Use Abstraction to Simplify Design
- *abstractions* are used to represent design at different levels of representation
	- low-level details are hidden to offer a simpler model at higher levels

##### Make the Common Case Fast
- generally more important to enhance performance for the common case compared to optimizing rare cases
	- ironically, the common case is generally simpler and easier to enhance
- this advice implies the common case is known
	- can only be possible with careful experimentation and measurement

##### Performance via Parallelism
- since the dawn of computing, computer architects have offered designs that get more performance by performing operations in parallel
##### Performance via Pipelining
- particular parallelism pattern
- a series of steps, where each part is completed independently
##### Performance via Prediction
- in some cases, it can be faster on average to guess and start working rather than wait until you know for sure
	- this assumes the mechanism to recover from a misprediction is not too expensive
##### Hierarchy of Memories
- programmers want memory to be fast, large and cheap
	- memory speed often shapes performance
	- capacity limits the size of problems that can be solved
	- cost of memory is often the majority of computer cost
- architects have found they can address these demands with the *hierarchy of memories*
	- the fast, smallest, and most expensive memory per bit at the top of the hierarchy, and the slowest, largest, and cheapest at the bottom
- caches give the programmer the illusion that main memory is nearly as fast the top of the hierarchy, and nearly as big and cheap as the bottom of the hierarchy
##### Dependability via Redundancy
- computer not only need to be fast, they must also be dependable
- systems can be made dependably by including redundant components that can take over when a failure occurs and to help detect failures
## Below Your Program
- a typical program may consist of millions of lines of code and rely on sophisticated software libraries that implex complex functions in support of the application
- the hardware in a computer can only execute extremely simple low-level instructions
- going from a complex application to the simple instructions hardware can execute is a great example of abstraction
![[Pasted image 20260326212635.png]]
- as we can see, these layers are arranged in a hierarchical fashion, with the applications being built upon the systems software, which is built upon the hardware
- there are two types of systems software central to every computing system today:
	- an operating system
		- interfaces between a user's program and the hardware
		- provides a variety of services and supervisory functions:
			- handling basic I/O operations
			- allocating storage and memory
			- providing for protected sharing of the computer among multiple applications using it simultaneously
	- a compiler
		- translate a program written in a high level language, such as C/C++ or Java, into instructions that can be executed by the hardware
#### From a High-Level Language to the Language of Hardware
- to speak to electronic hardware, you need to send electrical signals
	- the easiest signals for computers to understand are *on* and *off*
		- represented by *0* and *1*
			- we generally consider computer language to be in base 2 (as opposed to base 10)
			- each character is referred to as a *bit* or binary digit
	- computers will execute our commands exactly, which are called *instructions*
	- using numbers for both instructions and data is a foundation of computing
- the first programmers communicated to computers in binary numbers, but this was extremely tedious
	- eventually, a program was used to translate symbolic notation directly into binary
		- this program was called an *assembler*
- as an example, a programmer would write `add A,B` and the assembler woudl translate this notation into `1000110010100000`
- the name coined for this symbolic language is *assembly language*
- the binary language the machine understands is known as *machine language*
- while an improvement, assembly language is still a far cry from the notations humans would like to use
	- assembly language requires the programmer to write one line for every instruction the computer will follow
		- the programmer must think like the computer
- eventually, the creation of *high-level programming languages* such as C enabled for more powerful abstractions
![[Pasted image 20260326214514.png]]
- comparatively, the programmer can write a high level expression such as `A+B`, the compiler will compile that into the assembly language statement `add A,B`, which can be converted into an appropriate binary language statement
##### Benefits of high-level programming languages
- allow the programmer to think in a more natural language
	- programs look much more like text than columns of cryptic symbols
- allows languages to be designed according to their intended use
	- Fortran for scientific computation
	- Cobol for business data processing
	- Lisp for symbol manipulation
	- etc.
- improved programmer productivity
	- programs take less time to develop when they are written in languages that require fewer lines of code to express an idea
- programs can be independent of the computer on which they were developer
	- compilers and assemblers can translate high-level language programs to the binary instructions of any computer

### Under the Covers
- the underlying hardware in any computer performs the same basic functions
	- inputting data
	- processing data
	- storing data
#### The Five Components of a computer that perform the tasks of inputting, outputting, processing, and storing data
- *input devices* -  a mechanism through which the computer is fed information, such as a keyboard
- *output devices* - a mechanism that conveys the result of a computation to a user, such as a display, or to another computer
- *memory*
- *datapath*
- *control*
	- the datapath and control are sometimes considered one entity, called the *processor*
![[Pasted image 20260327073155.png]]
- most personal mobile devices uses *LCDs* to get a thin, low-power display
	- most contain rod-shaped molecules in a liquid that bend light entering the display
	- most use an *active matrix* that has a tiny transistor switch to precisely control current and make images
	- the image is composed of a matrix of picture elements, or *pixels*
		- screens consist of hundreds of thousands to millions of pixels, organized in a matrix
- computer hardware support for graphics consists of a *raster refresh buffer* (*frame buffer*), which stores the bit map
	- the image to be represented is stored in the frame buffer, and the bit pattern is read to the graphics display at the refresh rate
		- the objective of the bit map is to faithfully represent what should be displayed on the screen
![[Pasted image 20260327184049.png]]
- many touchscreens today use capacitive sensing
	- humans are electrical conductors, so covering the screen in a transparent conductor means that touching the screen distorts the electrostatic field of the screen
		- this changes the capacitance and can be interpreted by the computer attached to the touch screen
- *integrated circuit* - also called a *chip*
	- a device containing dozens to millions of transistors
- the processor is the active part of the computer
	- it follows the instructions and adds numbers, tests them, signals I/O devices to follow, and so on
	- often referred to as the *central processor unit (CPU)*
- logically, the processor is comprised of two main components
	- the *datapath* performs all arithmetic operations
	- *control* tells the datapath, memory, and I/O what to do according to the wishes of the program instructions
- the *memory* is the storage area in which programs are kept when they are running
	- it contains the data needed by the running programs
	- memory is built from *DRAM* chips
		- DRAM is short for *dynamic random access memory*
			- DRAM is memory built as an integrated circuit
				- it provides random access to any location
					- access time: ~50 nanoseconds
		- multiple DRAMs are used together to contain the instructions and data of a program
		- the random nature of DRAM ensures that memory accesses take basically the same amount of time regardless of what portion of the memory is read
			- this in contrast to sequential access, where access the beginning is much quicker than the end
- the processor contains one more type of memory - *cache memory*
	- cache memory is a small, fast memory that acts as a buffer for DRAM memory
	- caches are built using *static random access memory (SRAM)*
		- SRAM is faster and less dense than DRAM
			- thus more expensive
			- SRAM and DRAM are two layers of the memory hierarchy
- one of the most important abstractions is the interface between hardware and the lowest-level of softwrae
	- referred to as the *instruction set architecture* or simply the *architecture*
		- it includes anything programmers need to know to make a binary machine program owrk correctly
			- instructions, I/O devices, etc.
			- these instructions are generally encapsulated in the operating system, which enables application programmers to simply leverage the OS to accomplish these things
- the combination of the basic instruction set and the operating system interface provided for application programmers is called the *application binary interface (ABI)*
- instruction sets allow computer designers to talk about functions independent from the hardware that performs them
	- e.g. we can discuss a digital clock (keeping time, setting an alarm, etc.) independent from the physical clock hardware (quartz crystal)
	- in general, we also distinguish between architecture and an implementation of that architecture
>"Both hardware and software consist of hierarchical layers using abstraction, with each lower layer hiding details from the level above. One key interface between the levels of abstraction is the instruction set architecture - the interface between the hardware and low-level software. this abstract interface enables many implementations of varying cost and performance to run identical software

##### A Safe Place for Data
- computer memory is *volatile*, when power to the computer is lost, everything in memory is erased
- *nonvolatile memory* is a form of memory that retains data even in the absence of a power source
	- used to store programs between runs
- the term *main memory* or *primary memory* is used to refer to the volatile memory used to hold programs while in use
- *secondary memory* is used to refer to the nonvolatile memory used to store programs between runs
	- forms the next lower layer of the memory hierarchy
- *magnetic disks*, also called *hard disks* are a form of secondary memory composed of rotating platters coated with a magnetic coating mechanical
	- access times are ~5 to 20 milliseconds
	- dominated secondary memory since almost the dawn of computing
		- have recently been replaced with *flash memory*
			- cheaper and slower than DRAM but more expensive and faster than magnetic disks
			- more rugged and more power efficient than disks
			- tend to wear out over time after too many writes
##### Communicating with Other Computers
- networks are in a way, the backbone of current computer systems
- networked computers have several major advantages:
	- **Communication** - information is exchanged between computers at high speeds
	- **Resource sharing** - as opposed to each computer having its own I/O devices, computers on the network can share I/O devices
	- **Nonlocal access** - enables users to access computer resources they are not physically near
- networks vary in length and performance, with the cost of communication increasing according to the speed of communicaton and distance travelled
	- *Ethernet* can transfer 40 gigabits per second up to a kilometer long
		- useful to connect computers on the same floor of a building
		- example of a *local area network*
			- in comparison, *wide area networks* cross continents and are the backbone of the internet
				- are based on optical fibers and are leased from telecommunications companies

### Technologies for Building Processors and Memory
- processors and memory have improved at an incredible rate
![[Pasted image 20260327193051.png]]
- a *transistor* is simply an on/off switch controlled by electricity
- the *integrated circuit (IC)* combined dozens to hundreds of transistors into a single chip
	- to describe the tremendous increase in transistor numbers from hundreds to millions, the *very large-scale integrated circuit (VLSI)* was created
- for decades, the growth in DRAM capacity has quadrupled every 3 years
![[Pasted image 20260327193525.png]]
- the manufacture of a chip begins with *silicon*, an element found in sand
	- it does not conduct electricity well, and thus is considered a *semiconductor*
	- it is possible to transform silicon into different forms via a chemical process:
		- excellent conductors of electricity (using microscopic copper or aluminum wire)
		- excellent insulators from electricity (like plastic sheathing or glass)
		- areas that can conduct or insulate under special conditions
			- transistors fall under this category
- a VLSI circuit is billions of conductors, insulators and switches manufactured in a single small package
- the chip manufacturing process is critical to the cost of chips and important to computer designers
![[Pasted image 20260327194035.png]]
- process begins with a *silicon crystal ingot*, which a rod composed of a silicon crystal between 8 and 12 inches in diameter and 12-24 inches long
- an ingot is sliced into *wafers* no more than 0.1 inches thick
- these wafers go through a series of processing steps
	- in each step, patterns of chemicals are placed on each wafer, which creates the transistors, conductors and insulators referenced earlier
- a single microscopic flaw can result in that area of the wafer failing
	- this is referred to as a *defect*
	- it's virtually impossible to manufacture a perfect way
		- generally best to place many independent components on a single wafer
			- this wafer is diced into these components
				- the individual components are called *dies*
- dicing allows you to only discard the portions of the wafer that have defects on them
	- quantified by the *yield* of the process, which is the percentage of good dies from the total number of dies on the wafer
- the cost of an integrated circuit rises quickly as the die size increases
	- leads to a lower yield
	- also creates a smaller number of dies that fit on a wafer
- good dies are connected to the I/O pins of a package using a process called *bonding*
- The cost of an integrated circuit can be expressed in three equations:
	- $\text{Cost per die} = \frac{\text{Cost per wafer}}{\text{Dies per wafer} * yield}$
	- $\text{Dies per wafer} \approx \frac{\text{Wafer area}}{\text{Die area}}$
	- $yield = \frac{1}{(1+(\text{Defects per area} * \text{Die area}/2))^2}$
- depending on defect rate and the size of the die and wafer, costs are generally not linear in the die area
### Performance
- assessing the performance of computers can be challenging
	- scale and intricacy, as well as the wide range of performance improvement techniques make performance assessment difficult
- accurately measuring and comparing different computers is critical to purchasers and therefore to designers
#### Defining Performance
- what do we mean when we say one computer has better performance than another?
- if running a program on two different desktop computers, you might say the faster computer is the one that finishes first
- if running a datacenter, you might say the faster computer was the one that completed the most jobs
- as an individual computer user, you are likely most interested in reducing *response time* - the time between the start and the completion of a task
	- response time is also referred to as *execution time*
- we are generally also concerned with *throughput* - the number of tasks completed per unit time
	- also called *bandwidth*
- in general, the terminology *as fast* will be used when comparing computers quantitatively
	- performance and execution time are reciprocals, so increasing performance requires decreasing execution time
#### Measuring Performance
- time is the measure of computer performance
	- the computer that performs the same work in the least time is the fastest
	- execution time is measured in seconds per program generally, but can be measured in different ways