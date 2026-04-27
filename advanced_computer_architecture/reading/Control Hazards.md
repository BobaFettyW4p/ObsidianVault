- an instruction must be fetched at every clock cycle to sustain the pipeline, yet in our design the decision about whether to branch doesn't occur until the MEM pipeline stage
![[screenshots/Pasted image 20260426102025.png]]

- this delay in determining the proper instruction to fetch is called a *control hazard* or *branch hazard* in contrast to the *data hazards*
- control hazards are relatively simple to understand, occur less frequently than data hazards, and there is nothing as effective against control hazards as forwarding is against data hazards
	- thus, simpler schemes  are used
	- two primary schemes to resolve constol hazards and one optimization to improve these schemes
### Assume Branch Not Taken
- stalling until the branch is complete is too slow
	- one improvement is to predict that the branch will not be taken and thus continue execution down the sequential instruction stream
		- if the branch is taken, the instructions that are fetched and decoded must be discarded
		- execution continues at the branch target
			- if the branch is untaken half the time and cost to discard instructions is negligible, this can halve the cost of control hazards
			- to discard instructions, we change the control values to 0s, much as we did for a load-use data hazard
				- main difference is we must chang ethe three instructions in the IF, ID, and EX stages when the branch reaches the MEM stage
					- for load-use stalls, we simply change control to 0 in the ID stage let them continue through the pipeline as usual
				- discarding instructions means we must be able to flush instructions in the IF, ID, and EX stages of the pipeline
### Reducing the Delay of Branches
- one way to improve branch performance is to reduce the cost of the taken branch
	- up to this point, we have assumed the next PC for a branch is selected in the MEM stage
		- if we move the branch execution earlier in the pipeline, then fewer instructions need to be flushed
- MIPS was designed to support fast single-cycle branches that could be pipelined with a small branch penalty
	- the designers of MIPS observed that many branches rely on simple tests that do not require a full ALU operation and can be performed with a few gates at most
	- when a more complex branch decision is required, a separate instruction that uses an ALU to perform a comparison is required, a separate instruction that uses an ALU  to perform a comparison is required
- moving the branch decision up requires two actions to occur earlier:
	- computing the branch target address
	- evaluating the branch decision
	- the easy part is to move up the branch address calculation
		- we already have the PC value and the immediate field in the IF/ID pipeline register
			- just move the branch adder from the EX stage to the ID stage
				- branch target address calculation will be performed for all instructions, only used when needed
	- the harder part is the actual branch decision
		- for branch equal, we compare to the two registers read during the ID stage to see if they are equal
			- equality can be tested by exclusive ORing their respective bits and then ORing all the results
				- moving the branch test to the ID stage implies additional forwarding and hazard detection hardware
					- a branch dependent on a result still in the pipeline must work properly with this optimization
						- to implement branch on equal (and its invers), we need to forward to the equality test logic that operates during ID
						- there are two complicating factors to this:
							- during ID, we must decode the instruction, decide whether a bypass to the equality unit is needed, and complete the equality comparison so that is the instruction is a branch, we can set the PC to the branch target address
								- forwarding the operands of branches was formerly handled by the ALU forwarding logic
									- the introduction of the equality test unit in ID will require new forwarding logic
										- the bypassed source operands of a branch can come from either the ALU/MEM or MEM/WB pipeline latches
							- because the values in a branch comparison are needed during ID but may be produced later in time, it is possible that a data hazard can occur and a stall will be needed
								- if an ALU instruction immediately proceeding a branch produces one of the operands for comparison in the branch, a stall will be rquired
									- the EX stage for the ALU instruction will occur after the ID cycle of the branch
										- if a load is immediately followed by a conditional branch that is on the load result, two stall cycles will be required
											- a result from the load appears at the end of the MEM cycle but is needed at the beginning of ID for the branch
							- despite these difficulties, moving the branch execution to the ID stage is an improvement
								- reudces the penalty of a branch to only one instruction is the branch is taken
									- e.g. the one currently being fetched
###### Example
>Show what happens when the branch is taken in this instruction sequence, assuming the pipeline is optimized for branches that are not taken that we moved the branch execution to the ID stage

```
36  sub $10, $4, $8
40  beq $1, #3, 7 # PC-relative branch to 40+4+7*4=72
44  and $12, $2, $5
48  or  $13, $2, $6
52  add $14, $4, $2
56  slt $15, $6, $7
...
72  lw $4, 50($7)  
```
- this diagram shows what happens when a branch is taken:
![[screenshots/Pasted image 20260426110823.png]]


### Dynamic Branch Prediction
- assuming a branch is not taken is one simple form of *branch prediction*
	- in this case, we predict that branches are untaken and flush the pipeline when we are wrong
- for the simple five-stage pipeline, this approach with compiler-based prediction is likely adequate
	- in the case of deeper pipelines, the branch penalty increases when measured in clock cycles
		- with multiple issue, the branch penalty increases in terms of instructions lost
			- in an aggressive pipeline, a static prediction scheme will probably waste too much performance
				- with more hardware, it is possible to predict branch behavior during program execution
- one approach is to look up the address of the instruction to see if a branch was taken the last time this instruction was executed
	- if so, begin fetching new instructions from the same place as the last time
		- this is called *dynamic branch prediction*
	- one implementation of this approach is a *branch prediction buffer* or *branch history table*
		- a branch prediction buffer is a small memory indexed by the lower portion of the address of the branch instruction
			- contains a bit that says whether the branch was recently taken or not
	- this is the simplest sort of buffer
		- we don't know if the prediction is the right one, but this doesn't affect correctness
			- prediction is a hint that we hope is correct, so we start fetching in the predicted direction
				- if the hint is wrong, the incorrectly predicted instructions are deleted, the prediction bit is inverted and stored back
					- the proper sequence is then fetched and executed
- 1 bit prediction schemes have a performance shortcoming
	- even if a branch is almost always taken , we can predict incorrectly twice when it is not taken
		- consider a loop that runs 10 times
			- it will predict wrong on the final iteration that exits the loop
				- it will also predict wrong on the first iteration often enough
- ideally, the predictor accuracy would match the taken branch frequency for regularly taken branches
	- can be remedied via 2-bit prediction schemes
- a five-stage pipeline can turn a control hazard into a feature by redefining the branch
	- a delayed branch always executes the following instruction, but the second instruction following the branch will be affected by the branch
	- compilers and assemblers try to place an instruction that always executes after the branch in the *branch delay slot*
		- the branch delay slot is directly after a delayed branch instruction
			- MIPS will place an instruction that does not affect the branch in it
			- the software needs to make the successor instructions valid and useful
![[screenshots/Pasted image 20260426112743.png]]

- limitations on delayed branch scheduling arise from:
	- restrictions on instructions that are scheduled into the delay slots
	- our ability to predict at compile time whether a branch is likely to be taken or not
- delay branching was a simple and effective solution for a five-stage pipeline issuing one instruction each clock cycle
	- as processor go to longer pipelines and issuing multiple instructions per clock cycle, the branch delay becomes longer, and a single delay slot in insufficient
		- due to this, delayed branching has lost popularity compared to more expensive but more flexible dynamic approaches
			- the frowth in available transistors per chip has made dynamic production relatively cheaper
![[screenshots/Pasted image 20260426113048.png]]

- a branch predictor tells us whether or not a branch is taken, but still requires the calculation of the branch target
	- in the five-stage pipeline, this calculation takes one cycle
		- taken branches will incur a 1-cycle penalty
		- can be alleviated via delayed branches
			- another approach is to use a cache to hold the destination program or destination instruction using a *branch target buffer*
				- branch target buffers are generally organized as a cache with tags
					- makes it more costly than a simple prediction buffer
		- a 2-bit prediction scheme uses only info about a particular branch
			- researchers noticed using information about a local branch, and the global behavior of recently executed branches together yields greater prediction accuracy for the same number of prediction bits
				- called *correlating predictors*
					- a correlating predictor might have two 2-bit predictors for each branch
						- choice between predictors is made based on whether the last executed branch was taken or not taken
							- global branch behavior can be thought of as adding additional index bits for the prediction lookup
		- a more recent innovation was the use of *tournament predictors*
			- a tournament predictor uses multiple predictors tracking which predictor yields the best results
				- a typical one might contain two predictions for each branch, one based on local behavior and one based on global behavior
					- a selector will choose which predictor to be used for any specific prediction
						- the selector will operate similar to a 1 or 2 bit predictor and favor whichever predictor has been more accurate
- one way to reduce the number of conditional branches is to add *conditional move* instructions
	- instead of changing the PC with a conditional brach, the instruction conditionally changes the destination register of the move
		- if it fails, the move acts as a $\texttt{nop}$
			- one version of MIPS has two instructions called $\texttt{movn}$, move if not zero, and $\texttt{movz}$, move if zero
			- 