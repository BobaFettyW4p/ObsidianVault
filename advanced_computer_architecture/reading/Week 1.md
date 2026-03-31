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

![[screenshots/Pasted image 20260325204635.png]]

#### Welcome to the PostPC Era
- the PC is gradually being replaced by the *personal mobile device (PMD)*
![[screenshots/Pasted image 20260326202423.png]]
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
![[screenshots/Pasted image 20260326212635.png]]
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
![[screenshots/Pasted image 20260326214514.png]]
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
![[screenshots/Pasted image 20260327073155.png]]
- most personal mobile devices uses *LCDs* to get a thin, low-power display
	- most contain rod-shaped molecules in a liquid that bend light entering the display
	- most use an *active matrix* that has a tiny transistor switch to precisely control current and make images
	- the image is composed of a matrix of picture elements, or *pixels*
		- screens consist of hundreds of thousands to millions of pixels, organized in a matrix
- computer hardware support for graphics consists of a *raster refresh buffer* (*frame buffer*), which stores the bit map
	- the image to be represented is stored in the frame buffer, and the bit pattern is read to the graphics display at the refresh rate
		- the objective of the bit map is to faithfully represent what should be displayed on the screen
![[screenshots/Pasted image 20260327184049.png]]
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
![[screenshots/Pasted image 20260327193051.png]]
- a *transistor* is simply an on/off switch controlled by electricity
- the *integrated circuit (IC)* combined dozens to hundreds of transistors into a single chip
	- to describe the tremendous increase in transistor numbers from hundreds to millions, the *very large-scale integrated circuit (VLSI)* was created
- for decades, the growth in DRAM capacity has quadrupled every 3 years
![[screenshots/Pasted image 20260327193525.png]]
- the manufacture of a chip begins with *silicon*, an element found in sand
	- it does not conduct electricity well, and thus is considered a *semiconductor*
	- it is possible to transform silicon into different forms via a chemical process:
		- excellent conductors of electricity (using microscopic copper or aluminum wire)
		- excellent insulators from electricity (like plastic sheathing or glass)
		- areas that can conduct or insulate under special conditions
			- transistors fall under this category
- a VLSI circuit is billions of conductors, insulators and switches manufactured in a single small package
- the chip manufacturing process is critical to the cost of chips and important to computer designers
![[screenshots/Pasted image 20260327194035.png]]
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
	- time can be defined in different ways
		- the most straightforward is *wall clock time*, *response time*, or *elapsed time*
			- simply refers to the total time to complete a task
				- disk accesses
				- memory accesses
				- I/O activities
				- OS overhead
- computers are often shared, and so a processor may be working on many different tasks simultaneously
	- the system may optimize throughput as opposed to simply minimizing the elapsed time for one of the many programs it is processing
		- we often want to distinguish between elapsed time and the total amount of time the processor spends on a particular program's behalf
			- this is referred to as *CPU execution time* or *CPU time*
			- does not include time spent waiting for I/O or running other programs
				- response time will be the elapsed time, not the CPU time
				- CPU time can be further divided by the time spend in the program...
					- *user CPU time*
				- and time spent performing tasks on behalf of program
					- *system CPU time*
				- however, it is difficult to differentiate between user and system CPU time accurately
					- it's hard to assign responsibility for operating system activities to one specific user program
- we will use the term *system performance* to refer to elapsed time on an unloaded system and *CPU performance* to refer to user CPU time
- different applications are sensitive to different aspects of the performance of a computer system
	- some depend on I/O performance, which relies on both hardware and software
- while we generally care about time, it's convenient to sometimes think about performance in terms of other metrics
	- you may want to think about a computer using a measure that relates to how fast the computer can complete certain tasks
		- these time intervals are called *clock cycles* (*ticks*, *clock ticks*, *clock periods*, *clocks*, *cycles*)
			- the length of a *clock period* is both the time for a complete *clock cycle* (e.g. 250 picoseconds, 250 ps)and as the *clock rate* (e.g. 4 gigahertz, 4 GHz)
				- the clock rate is the inverse of the clock period
###### CPU Performance and Its Factors
- users and designers often examine performance using different metrics
	- if we could relate them, we could determine the effect of a design change on the performance as perceived by a user
- a simple formulate to relate the most basic metrics to CPU time:
	- $\text{CPU execution time for a program} = \text{CPU clock cycles for a program} * \text{Clock cycle time}$
- alternatively, as clock rate and clock cycle time are inverses:
	- $\text{CPU execution time for a program} = \frac{\text{CPU clock cycles for a program}}{\text{Clock rate}}$
- a hardware designer can improve performance by reducing the number of clock cycles requires for a program or by reducing the length of a clock cycle
	- however, many techniques that decrease the number of clock cycle also increase the clock cycle time

### Improving Performance Example
> A program runs in 10 seconds on computer A, which has a 2 GHz clock. We are trying to design computer B, which will run this program in 6 seconds. We will be able to increase the clock rate to do this, but this affects CPU design, causign computer B to require 1.2 times as many clock cycles as computer A for this program. What clock rate should be targeted?

- first, find the # of clock cycles required on computer A:
	- $\text{CPU time} = \frac{\text{CPU clock cycles}}{\text{Clock rate}}$
	- $10s = \frac{\text{CPU clock cycles}}{2 * 10^9 \frac{cycles}{second}}$
	- $\text{CPU clock cycles} = 10 * 2 * 10^9 \frac{cycles}{second} = 10 * 10^9$
- we can use this to find the CPU time for computer B:
	- $\text{CPU time} = \frac{1.2 * \text{CPU clock cycles}_A}{\text{Clock rate}_B}$
	- $6s = \frac{1.2 * 20 * 10^9\ cycles}{\text{Clock rate}_B}$
	- $\text{Clock rate}_B = \frac{1.2 * 20 * 10^9\ cycles}{6 seconds} = \frac{0.2 * 20 * 10^9\ cycles}{second} = \frac{4*10^9\ cycles}{second} = 4\ Ghz$

#### Instruction Performance
- the above example does not include any reference to the number of instructions needed for the program
	- however, the execution time must rely on the number of instructions in a program, not simply the run time
- execution time can be thought about that it equals the number of instructions executed multiplied by the average time per instruction
	- Therefore
		- $\text{CPU clock cycles} = \text{Instructions for a program} * \text{Average clock cycles per instruction}$
		- *Clock cycles per instruction* is often abbreviated as *CPI*
##### Using the Performance Equation
>We have two implementations of the same instruction set architecture. Computer A has a clock cycle time of 250 ps and a CPI of 2.0 for some program, and computer B has a clock cycle time of 500 ps and a CPI of 1.2 for the same program. Which computer is faster for this program and by how much?

- now each computer executes the same number of instructions. Let this number be $I$. First find the number of processor clock cycles for each computer:
	- $\text{CPU clock cycles}_A = I * 2.0$
	- $\text{CPU clock cycles}_B = I * 1.2$
- Now we can computer the CPU time for each computer
	- $\text{CPU time}_A = \text{CPU clock cycles}_A * \text{Clock cycle time}$
	- $= I * 2.0 * 250 ps = 500 * I ps$
- likewise for computer B:
	- $\text{CPU time}_B = I * 1.2 * 500 ps = 600 * I$
- clearly, computer A is faster
	- the amount faster is given by the ratio of the execution times
	- $\frac{\text{CPU performance}_A}{\text{CPU performance}_B} = \frac{\text{Execution time}_B}{\text{Execution time}_A} = \frac{600 * I ps}{500 * I ps} = 1.2$
	- we are able to conclude that computer A is 1.2 times as fast as computer B for this program

#### The Classic CPU Performance Equation
- $\text{CPU time} = \text{Instruction Count} * \text{CPI} * \text{Clock cycle time}$
- since the clock rate is the inverse of clock cycle time:
	- $\text{CPU time} = \frac{\text{Instruction count} * \text{CPI}}{\text{Clock rate}}$
- these formulas separate the three key factors taht affect performance
	- can be used to compare two different implementations or evaluate a design alternative

#### Comparing Code Segments
- a compiler designer is trying to decide between two code sequence for a particular computer, and has been provided these facts:
![[screenshots/Pasted image 20260329093006.png]]
- the compiler write must select between code sequences with the following instruction counts:
![[screenshots/Pasted image 20260329093030.png]]
- Sequence 1 executes 5 total instructions
- Sequence 2 executes 6 total instructions
- We can use the equation for CPU clock cycles based on instruction count and CPI to find the total number of clock cycles for each sequence
	- $\text{CPU clock cycles} = \sum_{i=1}^n(CPI * C_i)$
	- this yields a total of 10 clock cycles for sequence 1, and a total of 9 for sequence 2
	- this means code sequence 2 is faster overall, even though it takes more instructions
- we can then calculate the CPI for each individual code sequence
	- $\text{CPI}_1 = \frac{\text{CPU clock cycles}_1}{\text{Instruction count}_1} = \frac{10}{5} = 2.0$
	- $\text{CPI}_2 = \frac{\text{CPU clock cycles}_2}{\text{Instruction count}_2} = \frac{9}{6} = 1.5$
##### Big Picture
![[screenshots/Pasted image 20260329093642.png]]
- these factors are combined to yield execution time measured in seconds per program:
- $\text{Time} = \text{Seconds/Program} = \frac{\text{Instructions}}{\text{Program}} * \frac{\text{Clock cycles}}{\text{Instruction}} * \frac{\text{Seconds}}{\text{Clock cycle}}$
- the only complete and reliable measure fo computer performance is time
	- changing the instruction set to lower the instruction count may lead to an organization with a slower clock cycle time or higher CPI that offsets the improvement in instruction count
		- since CPI depends on the type of instructions executed, the code that executes the fewest number of instructions may not be fastest

#### Understanding Program Performance
- the performance of a program depends on the algorithm, the language, the compiler, the architecture, and the actual hardware
	- an algorithm can affect the instruction count, and CPI by determining the number of source program instructiosn executed
	- the programming language can obviously affect the instruction, and its features may also affect CPI
	- compiler efficiency also effects the instruction count and average cycles per instruction
		- the compiler determines the translation of the source language instructiosn into computer instructions
	- instruction set architecture affects all aspects of a computer performance because it affects the instructions needed for a function, the cost in cycles of each instruction, and the overall clock rate of the processor
- you might expect the minimum CPI is 1.0, but some processors fetch and execute multiple instructions per clock cycle
	- some designers invert CPI in lieu of IPC, *instructions per clock cycle*
		- if a processor executes 2 instructions per clock cycles, then it has an IPC of 2
			- $\therefore$, a CPI of 0.5

### The Power Wall
![[screenshots/Pasted image 20260329095237.png]]
- as the graph demonstrates, power and clock rate increased rapidly for decades, and then flattened off beginning in the early 2000s
	- they grew together because they are correlated, and the reason for the slowing is we have run into the practical power limit for cooling
- power provides a limit to what can be cooled, but the really critical resource in the PostPC era is energy
	- battery life can trump performance for mobile devices, and warehouse scale computers try to reduce the costs of powering and cooling 100k servers as the costs are incredibly high at this scale
	- similar to how measuring time in seconds is a safe measure of program performance, joules is considered to be a better measuer than a power rate like watts, which is joules/second
- the dominant technology for integrated circuits is called CMOS
	- complementary metal oxide semiconductor
	- in CMOS, the primary source of energy consumption is dynamic energy
		- energy that is consumed when transistors switch states from 0 to 1 and vice versa
			- $\text{Energy} \propto \text{Capacitative load} * \text{Voltage}^2$
				- this is the energy of a pulse during the logic transition from o -> 1 -> 0 or 1 -> 0 -> 1
				- the energy of a single transition is:
					- $\text{Energy} \propto .5 * \text{Capacitative load} * \text{Voltage}^2$
		- the power related per transistor is the product of energy of a transition and the frequence of transitions:
			- $\text{Power} \propto .5 * \text{Capacitative load} * \text{Voltage}^2 * \text{Frequency switched}$
		- frequency switched is a function of the clock rate
		- capacitative load per transistor is a function of the number of transistors connected to an output
			- *fanout*
			- and the technology, which determines the capacitance of both wires and transistors
##### Relative Power
- suppose we developed a new, simpler processor that has 85% of the capacitative load of the more complex older process
	- assume it has adjustable voltage that can reduce voltage 15% compared to processor B
		- this results in a 15% shrink in frequency
- what is the impact on dynamic power?
$$
\frac{Power_{new}}{Power_{old}} = \frac{(\text{Capacitative load} * 0.85)*(Voltage * 0.85)^2 * (\text{Frequency switched}*.85)}{\text{Capacitative load} * \text{Voltage}^2 * \text{Frequency switched}}
$$
- thus, the power ratio is $0.85^4 = 0.52$
	- the new processor uses ~ half the power of the old processor
- the real issue is that lowering voltage makes transistors too leaky
	- even today, about 40% of the power consumption in server chips is due to leakage
		- chip designers are adding large devices to chips to increase cooling
		- turn off parts of chips that are not used in a given clock cycle
		- there are expensive ways to cool chips and raise power, but they are generally too expensive for consumer devices
- computer designers hit a power wall, and so had to choose a new path from the way they designed microprocessors
- although dynamic energy is the primary source of energy consumption in CMOS, static energy consumption because of leakage current that flows even when a transistor is off
	- increasing the number of transistors increases power dissipation, even if the transistors are always off
		- there are techniques deployed to control leakage, but it's hard to lower voltage further
- Power is a challenge for two reasons:
	- power must be brought in and distributed around the chip
		- modern microprocessors use hundred of pins just for power and ground
		- multiple levels of chip interconnect are used solely for power and ground distribution to different portions of the chip
	- power is dissipated as heat and must be removed
		- server chips can burn more than 100 watts, and cooling the chip and system is a major expense in Warehouse Scale Computers
### The Sea Change: The Switch from Uniprocessors to Multiprocessors
- the power limit forced a dramatic change in the design of microprocessors
![[screenshots/Pasted image 20260329102523.png]]
- there was a shift from a single processor with faster and faster clock times, to systems that ship with more and more processors that can operate in parallel
	- benefit is more on throughput that response time
	- to differentiate, companies generally refer to processor as cores
		- such processors are considered to be multicore processors
>"Up to now, most software has been like music written for a solo performer; with the current generation of chips we're getting a little experience with duets and quartetes and other small ensembles; but scoring a work for large orchestra and chorus is a different kind of challenge"
- in the past, programmers could rely on innovations in hardware, architecture, and compilers to double performance of their programs every 18 months
	- this yielded considerable improvements in reponse time without any modifications to the program
	- today, programmers must rewrite their programs to take advantage of multiple processors in order to improve performance
- parallelism has always critical to performance in computing, but it was often hidden
	- *pipelining* is a technique that runs programs faster by overlappting the execution of instructions
		- an example of *instruction-level parallelism*
			- the parallel nature of hardware is abstracted away so the programmer and compiler can think of the hardware as executing instructions sequentially
		- forcing programmers to be aware of and account for the parallel nature of hardware was long considered a "third rail" of computer architecture
			- in the past, companies that relied on this behavior generally failed
- Why is it so hard for programmers to write explicitly parallel programs?
	- by definition, parallel programming is performance programming
		- more difficult
		- not only must the program be correct, sovle an important problem, and provide a useful interface to people or other programs that invoke it, the program must also be fast
			- if you don't need performance, just write a sequential program
	- the programmer must divide an application so that each processor has roughly the same amount to do at the same time
		- the overhead of scheduling and coordination needs to not expend the potential performance benefits from parallelism
			- the load must be balanced evenly to receive performance benefits

### Real Stuff: Benchmarking the Intel Core i7
- a computer that runs the same programs day in and day out is a perfect candidate to evaluate a new computer
	- the set of programs would constitute a *workload*
- to evaluate two systems. the user would run the workload on both and compare the runtime
	- most users are not in this situation
		- they must use other methods that measure the performance of a candidate computer
		- generally done by evaluating a computer using a set of *benchmarks*
			- programs chosen specifically to measure performance
			- benchmarks form a workload that will ideally predict the performance of the actual workload
- SPEC (System Performance Evaluation Cooperative) is funded and supported by a number of computer vendors to create standard benchmarks for modern computer systems
![[screenshots/Pasted image 20260329120900.png]]
- to simplify marketing, SPEC has created a metric that is a single number that accounts for all of these metrics
- SPEC has also created a metric that measures power
![[screenshots/Pasted image 20260329120959.png]]
### Fallacies and Pitfalls
>"Science must begin with myths, and the criticism of myths"

##### Pitfall: Expecting the improvement of one aspect of a computer to increase overall performance by an amount proportional to the size of the improvement

- the opportunity is affected by how much time an event consumes
	- suppose a program runs in 100 seconds, with multiply operations responsible for 80 seconds of the runtime. How much does multiplication have to improve performance by five times?
		- to solve this, we must consider *Amdahl's Law*
$$
\text{Execution time after improvement} = \frac{\text{Execution time affected by improvement}}{\text{Amount of improvement}}+\text{Execution time unaffected}
$$
$$
20\ seconds = \frac{80\ seconds}{n} + 20\ seconds
$$
$$
0 = \frac{80\ seconds}{n}
$$
As such, it is not possible to improve performance by this amount
##### Fallacy: Computer at low utilization use little power
- power efficiency at low utilizations matter because server workloads vary
	- servers at Google utilize between 10% and 50% most of the time, and operate at 100% less than 1% of the time
		- a good sysadmin aims to minimize the amount of time servers run at 100%
	- specifically configured computers aiming to achieve the best results use 33% of the power at 10% of the load
- since workloads vary but use a large fraction of the peak power, Luiz Barroso and Us Holze argue hardware should be redesigned to achieve *energy-proportional computing*
	- if future servers used 10% of the peak power at 10% fo the workload, electricity bills would plummet
##### Fallacy: Designing for performance and designing for energy efficiency are unrelated goals
- since energy is power over time, oftentimes hardware or software optimizations that take less time save energy overall
	- all of the rest of the computer consumes energy while the program is running
		- even if the optimized portion uses a little more energy, the reduced time can save the energy of the entire system
##### Pitfall: Using a subset of the performance equation as a performance metric
- previously, we have discussed why basing performance on only one of clock rate, instruction count, or CPI is dangerous
	- it's also a mistake to only use two
		- may be valid in a limited content
	- nearly all proposed alternatives to the use of time, have eventaully led to misleading claims, distorted results, or incorrect interpretations
- one time alternative is *MIPS (million instructions per second)*
$$
\text{MIPS} = \frac{\text{Instruction count}}{\text{Execution time} * 10^6}
$$
- MIPS is an instruction execution rate
	- specifies performance inversely to execution time
		- faster computers have a higher MIPS rating
	- easy to understand (faster computers mean bigger MIPS)
- there are three problems with using MIPS as a measure for comparing computers
	- MIPS specifies the instruction execution rate, but does not take into account the capability of the instructions
		- we cannot compare computers with different instruction sets using MIPS
	- MIPS caries between programs on the same computer
		- a computer cannot have a single MIPS rating
$$
\text{MIPS} = \frac{\text{Instruction count}}{\frac{\text{Instruction count}*\text{CPI}}{\text{Clock rate}}*10^6} = \frac{\text{Clock rate}}{\text{CPI}*10^6}
$$

### Concluding Remarks
>"Where the ENIAC is equipped with 18,000 vacuum tubes and weighs 30 tons, computers in the future may have 1,000 vacuum tubes and perhaps weigh just 1.5 tons"

- it is difficult to predict what level of cost/performance computers will have in the future
	- it's a safe bet they'll be much better than they are today
- both hardware and software designers construct computer systems in hierarchical layers
	- each layer hides details from the level above
		- abstraction is fundamental to understanding today's computer systems, but designers cannot limit themselves to understanding a single layer of the abstraction
- the most important abstraction is the interface between hardware and low-level software
	- called the instruction set architecture
		- maintaining it as a constant enables many implementations of the architecture to run identical software
			- a downside is this architecture may not allow for the inclusion of innovations that would require this interface to change
- there is a relaible method to determine and report performance
	- the execution time of real programs is used as the metric
$$
\frac{Seconds}{Program} = \frac{Instructions}{Program} * \frac{\text{Clock Cycles}}{Instruction}*\frac{Seconds}{\text{Clock cycle}}
$$
- individually, the factors do not determine performance, only the product, which equals execution time, is a reliable measure of performance