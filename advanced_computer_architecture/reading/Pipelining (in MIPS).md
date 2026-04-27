### Pipelining Example
- canonical example: doing laundry
	- 4 loads to do
		- washer takes 30 minutes
		- dryer takes 40 minutes
		- folding takes 20 minutes
![[screenshots/Pasted image 20260426072946.png]]
- performing laundry sequentially makes each load take 90 minutes, so the entire job takes 6 hours
![[screenshots/Pasted image 20260426073016.png]]
- if the loads are appropriately pipelined, we can but the time for the total job down to 3.5 hours
##### Latency vs. Throughput
- in both cases, the latency for a single load is unchanged
	- it takes 90 minutes from putting the load in the washer to finishing folding
- the throughput however, is much improved
	- in the un-pipelined example, the throughput is $0.66$ loads per hour
	- the pipelined example is $1.14$ loads per hour
- pipeline performance is limited by its slowest stage
- the goal is to balance time for each stage as much as possible
- would the speedup increase if we had more steps?
$$
time_{pipeline} = time_{non-pipeline}/\text{pipe stages}
$$
- assumptions that are made:
	- stages are perfectly balanced
	- pipeline occurs under ideal conditions
	- startup and wrap-up costs are amortized
- these assumptions are made by all modern processors
	- it is how they achieve one instruction complete per cycle throughput at very high clock rates
	- you could also have one long clock cycle and have 1 CPI throughout, but this is a losing strategy
### Five Stages of a MIPS Instruction
- instructions can be decomposed in many different ways into a sequence of sub-steps
- a classic and natural decomposition:
	- IF: Instruction Fetch
	- ID: Registers Fetch and Instruction Decode
	- EX: Calculate the memory address or arithmetic operation
	- MEM: Read the data from the data memory
	- WB: Write the data back to the register file
![[screenshots/Pasted image 20260426080304.png]]

#### Instruction Fetch (IF)
- send the program counter (PC) to memory and fetch the current instruction from memroy
- update the PC to the next sequential PC by adding 4 (each instruction in MIPS is 4 bytes) to the PC

#### Instruction Decode (ID)
- decodes the instruction and reads the registers corresponding to the register source specifiers from the register file
- do the equality test on the registers as they are read, for a possible branch
- sign-extend the offset field of the instruction in case it is needed
- compute the possible branch target address by  adding the sign-extnended offset to the incremented PC
- decoding is done in parallel with reading registers, which is possible because the register specifiers are at a fixed location in a RISC architecture

#### Execution (EX)
- the ALU operates on the operands prepared in the prior cycle, performing one of three functions depending on the instruction type:
	- **memory reference** - the ALU adds the base register and the offset to form the effective address
	- **register-register ALU instruction** - the ALU performs the operation specified by the ALU opcode on the values read from the register file
	- **register-immediate ALU instruction** - the ALU performs the operation specified by the ALU opcode on the first value read from the register file and the sign-extended immediate

#### Memory access (MEM)
- if the instruction is a load, the memory does a read using the effective address computed in the previous cycle
- if it is a store, then the memory writes the data from the second register read from the register file using the effective address

#### Write Back (WB)
- Register-Register ALU instruction or load instruction
	- write the result into the regi

### The MIPS five stage pipeline
- in this implementation:
	- branch instructions require 2-3 cycles
	- store instructions require 4 cycles
	- all other instructions require 5 cycles
- for a branch frequency of 12% and a store frequency of 10% (pretty typical), a typical instruction distribution leads to an overall CPI of 4.54:
- it should be noted that this is not the optimal breakdown of tasks for a non-pipelined MIPS implementation
### Conventional Pipelined Execution Representation
![[screenshots/Pasted image 20260426082613.png]]
- new instructions begin before the most recently issued ones complete
- note all instructions in this pipeline take the full five stages
- other reasons for stalls will be discussed shortly
##### Example
> Consider the unpipelined processor in the previous section. Assume that it has a 1 ns clock cycle and uses 4 cycles for ALU operations and branches and 5 cycles for memory operations. Assume the relative frequencies of these operations are 40%, 20%, and 40%, respectively. Suppose that due to clock skew and setup, pipelining the processor addsd 0.2 ns of overhead to the clock. How much speedup in the instruction execution rate will be gained from a pipeline?

- the average instruction execution time on the unpipelined processor is:
$$
\text{Average instruction execution time} = \text{clock cycle} \times \text{Average CPI}
$$
$$
= 1\ ns \times [(40\% + 20 \%) \times 4 + 40\% \times 5]
$$
$$
= 1\ ns \times 4.4
$$
$$
= 4.4\ ns
$$
- in the pipelined implementation, the clock runs at the speed of the slowest stage plus overhead
	- $1 + 0.2$ or $1.2\ ns$
		- this is the average instruction execution time
- Thus, the speedup from pipelining is:
$$
\frac{4.4\ ns}{1.2\ ns} = 3.66 \text{ times speedup}
$$

### Pipeline Hazards
##### Structural Hazards
- when two stages of pipeline at a given time need access to the same hardware resource
![[screenshots/Pasted image 20260426084515.png]]

- this hazard can be avoided by writing to the register file on clock downbeat and reading on the upbeat like so:
![[screenshots/Pasted image 20260426084822.png]]

##### Example
>Suppose that data references consitute 40% of the mix, and that the ideal CPI of the pipelined processor ignoring the structural hazard is 1. Assume that the processor with the structural hazard has a clock rate that is 1.05 times higher than the clock rate of the processor without the hazard. Disregard any other performance losses, is the pipeline with or without the structural hazard faster, and by how much?

$$
\text{Average instruction time} = \text{CPI} \times \text{Clock cycle time}
$$
- since it has no stalls, the average instruction for the ideal processor is simple the $\text{Clock cycle time}_{ideal} = 1$ 
- the processor with the structural hazard is as follows:
$$
CPI_{hazard} = 1 + (0.40 \times 1) = 1.4
$$
- since it's clock rate is 1.05 times faster, its clock cycle time is shorter:
$$
\text{Clock cycle time}_{hazard} = \frac{\text{Clock cycle time}_{ideal}}{1.05}
$$
- therefore:
$$
\text{Avg instruction time}_{hazard} = 1.4 \times \frac{\text{Clock cycle time}_{ideal}}{1.05} = 1.333 \times \text{Clock cycle time}_{ideal}
$$
- the processor without the structural hazard is faster by about 33%
	- the higher clock rate of the hazard pipeline isn't enough to overcome the stall penalty

###### Data Hazards
- may occur when one requires as input the result of a previous instruction
```
add    $s0, $t0, $t1   # add the value in register t1 to the value in register t0 and store the result in register s0
sub    $t2, $s0, $t3   # add the value in register t3 to the value in register s0 and store the result in register t2
```
- will $\texttt{s0}$ be ready in time?
![[screenshots/Pasted image 20260426095131.png]]

- special pipeline registers are needed to save state between clock cycles and facilitate pipeline forwarding, as shown:
![[screenshots/Pasted image 20260426095157.png]]

- hazards can be reduced by pipeline forwarding:
![[screenshots/Pasted image 20260426095228.png]]

![[screenshots/Pasted image 20260426095245.png]]

- in some cases, pipeline stalls are unavoidable:
![[screenshots/Pasted image 20260426095306.png]]
##### Control hazards
- occur at branch statements where the result is not known until it is executed
	- without knowing whether a branch is taken, the next instruction is unknown, resulting in a stall
```
add   $t4, $t5, $t6
beq   $t1, $t2, addr   # if $t2=$t1, branch to the instruction stored in address                          addr
lw    $t3, 300($t0)    $ load the value stored at specified address into                                  register t3
```
- in the above example, the $\texttt{lw}$ instruction can't be executed until the branch is evaluated
	- this takes several cycles
![[screenshots/Pasted image 20260426095529.png]]
- in the above chart, this is a control hazard that means we must wait until the decision is clear
	- will cost 2 cycles where the processor must wait before proceeding
![[screenshots/Pasted image 20260426095636.png]]
- it is possible for the processor to predict the outcome of a branch instruction
	- if the processor predicts correctly, there are 0 lost cycles

![[screenshots/Pasted image 20260426095756.png]]

![[screenshots/Pasted image 20260426095810.png]]
- another behavior in response to branches is called *delayed branch*
	- it redefines branch behavior
		- the processor attempts to restructure the pipeline to place the branch in a place where it can process without causing a stall
		- if the processor can find other instructions to process while waiting for the branch to process, there is a 0 clock cycles per branch instruction impact
![[screenshots/Pasted image 20260426095922.png]]

![[screenshots/Pasted image 20260426100116.png]]
##### Example
> For a deeper pipeline, such as that in a MIPS R4000, it takes at least three pipeline stages before the branch-target address is known and an additional cycle before the branch condition is evaluated, assuming no stalls on the registers in the conditional comparison. A three-stage delay leads to the branch penalties for the three simplese prediction schemes listed below. Find the effective CPI arising from the branches for this pipeline, assuming the following frequencies:

![[screenshots/Pasted image 20260426100249.png]]

- we find the CPIs by multiplying the relative frequency of unditional, conditional untaken, and conditional taken branches by the respective penalties. The results are seen below:
![[screenshots/Pasted image 20260426100307.png]]

- the differences among the schemes are substantially increased with this longer delay
	- if the base CPI were 1 and branches were the only source of stalls, the ideal pipeline would be 1.56 times faster than a pipeline that used the stall-pipeline scheme. Thus, the predicted untaken scheme would be $1.56/1.38 = 1.13$ times better than the stall-pipeline scheme under the same assumptions

### Branch Prediction
- 1 bit predictor
	- predicts well
		- branches always taken
		- branches always not taken
		- taken >> not taken
		- not taken >> taken
	- each anomaly yields two mis-predictions
		- need long loops and not too many anomalies to get good performance
- basic 2 bit predictor
	- 4 states
		- the higher bit is the prediction bit, the lower bit is conviction
	- for each branch:
		- predict taken or not taken
		- if the prediction is wrong two consecutive times, change the prediction
	- $\texttt{00}$ = strong not taken
	- $\texttt{01}$ = weak not taken
	- $\texttt{10}$ = weak taken
	- $\texttt{11}$ = strong taken
- correlating predictor
	- good with patterns
		- multiple 2-bit predictors for each branch
		- one for each possible combination of outcomes of preceding $n$ branches
- local predictor
	- multiple 2-bit predictors for each branch
	- one for each possible combination of outcomes for the last $n$ occurrences of this branch
- tournament predictor
	- combine correlating predictor with local predictor
![[screenshots/Pasted image 20260426100917.png]]

### Dynamic Scheduling
- hardware rearranges order of instructions to reduce stalls while maintaining data flow
	- advantages
		- compiler doesn't need to have knowledge of microarchitecture
		- handles cases where dependencies are unknown at compile time
	- disadvantage
		- substantial increase in hardware complexity
		- complicates exceptions