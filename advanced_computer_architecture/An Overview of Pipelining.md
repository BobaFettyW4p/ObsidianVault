- *pipelining* - an implementation technique where multiple techniques are overlapped in execution
	- today, pipelining is nearly universal
![[Pasted image 20260421204621.png]]

- the pipelining paradox is that the time from placing a single dirty sock in the washer until it is cleaned, dried, folded, and put away is not shortened by pipelining
	- pipelining is faster for many loads as everything works together in parallel
		- does not shorten the time to do one load of laundry, but speeds up the time to do many loads of laundry
- the principles behind pipelining apply to processors where we pipeling instruction-execution
- MIPS instructions classically take 5 steps
	- fetch the instruction from memory
	- read registers while decoding the instruction
		- the regular format of MIPS instructions allows reading and decoding to occur simultaneously
	- execute the operation or calculate an address
	- access an operand in data memory
	- write the result into a register
### Single cycle vs pipelined performance
- all pipeline stages take a single cycle

- it is possible to turn the pipelining speed-up discussion into a formula
	- if the stages are perfectly balanced, then the time between instructions is equal to:
$$
\text{Time between instructions}_{pipelined} = \frac{\text{Time between instructions}_{nonpipelined}}{\text{Number of pipe stages}}
$$
- under ideal conditions and with a large number of instructions, the speed up from pipelining is approximately equal to the number of pipe stages
	- a five stage pipeline is nearly 5x fatser
	- in practice, this is generally not possible - stages may not be perfectly balanced
		- the actual practice of pipelining introduces some overhead
![[Pasted image 20260422210521.png]]

![[Pasted image 20260422210551.png]]

 - as our example proves, this is not always true
	 - our example should have a 4x speedup, but it's $2400/1400 = 1.7x$
	- at least partially because the number of instructions was not large
- consider an option where we add 1,000,000 instructions
	- tin the pipelined example, each instruction adds 200 ps to the execution time
		- gives us a pipelined execution time of $1,000,000 * 200 + 1400 = 200,001,400$
		- adding the same instructions to the non-pipelined gives us $1,000,000 * 800 + 2400 = 8,002,400$
		- Thus:
$$
\frac{800,002,400}{200,001,400} = \frac{800}{200} = 4.00
$$
#### Designing Instruction Sets for Pipelining
- all MIPS instructions are the same length
	- makes it much easier to fetch instructions in the first pipeling stage and decode them in the second stage
		- in x86, instructions vary from 1-15 bytes, which makes pipelining significantly more challenging
			- recent implementations actually translate x86 instructions into simple operations that look like MIPS instructions and then pipeline them
- MIPS has only a few instruction formats
	- source register fields are located in the same place in each instruction
		- the second stage can begin reading the register file at the same time that the hardware is determining what kind of hardware is fetched
			- if they weren't , stage 2 would need to be split, necessitating more pipeline stages
- memory operands only appear in loads or stores in MIPS
	- we can use the execute stage to calculate the memory address and then access memory in the following stage
- operands must be aligned in memory
	- we don't have to worry about a single data transfer instruction requiring two data memory accesses
		- the requested data can be transferred between processor and memory in a single pipeline stage
### Pipeline Hazards
- there are situations in pipelines where the next instruction cannot execute in the next clock cycle
	- these are called *hazards*
- *structural hazard*
	- in a structural hazard, the hardware cannot support the combination of instructions that we want to execute in the same clock cycle
	- the hardware cannot support the combination of instructions we want to execute in the same clock cycle
	- as the MIPS instruction set was designed to be pipelined, it is fairly easy for designers to avoid structural hazards when designing a pipeline
- *data hazards*
	- data hazards occur when the pipeline must be stalled as one step must wait for another to complete
		- generally arise with the dependence of one instruction on an earlier one that is still in the pipeline
		- suppose we have an add instruction followed immediately by a subtract instruction that uses the sum:
```
add $s0, $t0, $t1
sub $t2, $s0, $t3
```
- the add instruciton doesn't write the result until the fifth stage
	- so the second subtract instruction won't apply until three cycles later
	- the compilers may be able to fix this to an extent, but the result will not be satisfactory
- the primary solution is based on the idea that we don't need to wait for the instruction to complete before trying to resolve the data hazard
	- as soon as the ALU creates the sum for the add, we can supply it as an input for the subtract
- adding extra hardware to retrieve the missing item early from internal resources is called *forwarding* or *bypassing*

#### Forwarding with Two Instructions
- for the two instructions above, the datapath using the five stages of the pipeline is as follows:
![[Pasted image 20260423194757.png]]

- the connection to forward the value in $\texttt{s0}$ after the execution stage o the add instruction as input to the execution stage of the $\texttt{sub}$ instruction

![[Pasted image 20260423195154.png]]

- forwarding paths are only valid if the destination stage is later in time than the source stage
	- e.g. there cannot be a valid forwarding path from the output of the memory access stage in the first instruction to the input of the execution stage
		- that would mean going back in time
- forwarding works well, but cannot prevent all pipeline stalls
- a pipeline stall initiated to resolve a hazard is called a *pipeline stall* or *bubble*

#### Example
- consider the following code segment
```
a = b + e;
c = b + f;
```
- the generated MIPS code, assuming all variables are in memory and are addressable as offsets from $\texttt{\$t0}$:
```
lw    $t1, 0($t0)
lw    $t2, 4($t0)
add   $t4, $t1, $t2
sw    $t3, l2($t0)
lw    $t4, 8($t0)
add   $t5, $t1, $t4
sw    $t5, l6($t0)
```

- both $\texttt{add}$ instructions have a hazard because of their respective dependence on the preceding $\texttt{lw}$ instruction
	- bypassing also eliminates several other potential hazards
		- the dependence of the first $\texttt{add}$ on the first $\texttt{lw}$
		- any hazards for store instructions
		- this resolution eliminates both:
```
lw    $t1, 0($t0)
lw    $t2, 4($t0)
lw    $t4, 8($t0)
add   $t4, $t1, $t2
sw    $t3, l2($t0)
add   $t5, $t1, $t4
sw    $t5, l6($t0)
```

- each MIPS instruction writes at most one result and does this in the last stage of the pipeline
	- forwarding is harder if there are multiple results to forward per instruction
		- or if there is a need to write a result early on in instruction execution
- *control hazard*
	- arises from the need to make a decision based on the results of one instruction while others are executing
	- when the proper instruction cannot execute in the proper pipeline clock cycle because the instruction that was fetched
	- consider a branch instruction
		- we must begin fetching the instruction following the branch on the next clock cycle
			- however, the pipeline cannot know what the next instruction should be since it only just received the branch instruction
			- one solution is to stall immediately after we fetch a branch instruction until the processor knows what to do with it
			- we can put in extra hardware to test registers, calculate the branch address, and update the PC during the second stage of the pipeline
				- if we do this, the pipeline will look like this:
![[Pasted image 20260424172128.png]]
- the $\texttt{lw}$ instruction, which is only executed if the branch fails, is delayed 200 ps before starting
##### Performance of "Stall on Branch"
> Estimate the impact on the clock cycles per instruction (CPI) of stalling on branches. Assume all other instructions have a CPI of 1

- branches are 17% of the instructions
	- since other instructions have a CPI of 1, and branches take one additional clock cycle, we see a CPI of 1.17, hence: $\frac{1.17}{1} = 1.17$
- if we cannot resolve the branch in the second stage (this is often the case for longer pipelines), then we'd see an even large slowdown if we stall on branches
	- this option costs too much for most computers
		- motivates a second solution to the control hazard:
			- if you're pretty sure you have the right formula, just predict that it will work and proceed while waiting for the calculation to complete
				- does not slow down the pipeline when you are correct
					- when you are wrong, you need to redo the calculations
- computers do indeed use prediction to handle branches
	- a simple approach is to predict that branches will always be untaken
![[Pasted image 20260424173253.png]]
- a more sophisticated version of *branch prediction* will have some branches predicted as taken and some as untaken
	- branches that are the end of loops may always be predicted to be taken, as we know loops will iterate many times and only terminate once
- this approach does not provide for the individuality of a specific branch instruction
	- dynamic hardware predictors guess based on the behavior of each branch and may change predictions over the course of a program
		- one approach is to keep a history of each branch as taken or untaken, and then using past behavior to predict the future
			- this can lead to over 90% accuracy
			- when the guess is wrong, the pipeline control must ensure the instructions for the wrongly guessed branch have no effect and the pipeline must restart from the correct branch
		- a third approach is called *delayed decision*
			- the solution actually used by MIPS
			- the processor processes the next sequential instruction, with the branch occurring after one instruction delay
				- MIPS places an instruction immediately after the delayed branch instruction so its not affected by the branch, and then a taken branch changes the address of the instruction that follows this instruction
### Pipeline Overview Summary
- pipelining is a technique that exploits *parallelism* among instructions in a sequential instruction stream
	- it has the advantage that it is fundamentally invisible to the programmer
- outside of the memory system, the effective operation of the pipeline is the most important factor in determining the CPI of the processor, and hence processor performance
- understanding the performance of a modern multiple-issued pipelined processor is complex and requires understanding more than just the issues that arise in a simple pipelined processor
	- structural, data, and control hazards remain important in both simple pipelines and more sophisticated ones
- in modern pipelines, structural hazards usually resolve around the floating-point unit, which may not be fully pipelined
	- control hazards are more of a problem in integer programs
		- tend to have higher branch frequencies as well as less predictable branches
	- data hazards can be performance bottlenecks in both integer and floating-point programs
	- it is generally easier to deal with data hazards in floating point programs because the lower branch frequency and more regular memory access patterns allow the compiler to schedule instructions to avoid hazards
		- it is more difficult to perform optimizations in integer programs that have less regular memory access
			- more use of pointers
- Pipelining increases the number of simultaneously executing instructions and the rate at which instructions are started and completed
	- pipelining does the reduce the time it takes to execute an individual instruction
		- pipelining improves instruction throughput rather than individual instruction execution time or latency
- instruction sets can either simplify or make life harder for pipeline designers, who must cope with structural, control, and data hazards
	- branch prediction and forwarding help make a computer fast while still getting the right answers