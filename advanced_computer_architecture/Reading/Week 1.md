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