![[Pasted image 20260424195945.png]]
- this is the single-cycle datapath with the pipeline stages identified
- if you divide an instruction into five stages means a five stage pipeline, which means up to five instructions will be in execution during any single clock cycle
	- this means the datapath must be divided into five pieces, with each corresponding to a stage of instruction execution
		- IF: Instruction fetch
		- ID: Instruction decode and register file read
		- EX: Execution or address calculation
		- MEM: Data memory access
		- WB: write back
		- instructions generally move through each of the five stages sequentially as they complete execution
			- there are two main exceptions
				- write-back places the result back into the register file in the middle of the datapath
				- the selection of the next value of PC chooses between the incremented PC and the branch address from the MEM stage
				- the first right to left flow data can lead to data hazards
					- the second leads to control hazards
- one way to show what happens in pipelined execution is to pretend each instruction has its own datapath
	- we can then place these datapath on a timeline to show this relationship
![[Pasted image 20260424201226.png]]
- this figure seems to suggest that three instructions require three datapaths
	- these datapaths can be placed on a timeline to show their relationship
![[Pasted image 20260424201911.png]]

- the above shows the pipelined datapath with the pipeline registers highlighted
- all instructions advance during each clock cycle from one pipeline register to the next
	- the registers are named for the two stages separated by that register
		- e.g. the pipeline register between the $IF$ and $ID$ stages if called $IF/ID$
- there is no pipeline register at the back of the write-back stage
	- all instructions must update some state in the processor, so a separate pipeline register is redundant to the state that is updated
		- all instructions must update some state in the processor
	- every instruction updates the PC
		- incrementing it
		- setting it to a branch destination address
![[Pasted image 20260425124004.png]]

![[Pasted image 20260425124019.png]]![[Pasted image 20260425124259.png]]

- load and store illustrate a key point: each logical component of the datapath, such as instruction memory, register read ports, ALU, data memory, and register write port, can only be used within a single pipeline stage
	- otherwise, a structural hazard is created
	- these components and their control can be associated with a single pipleine stage
![[Pasted image 20260425124457.png]]

- we need to preserve the destination register number in the load instruction to prevent bugs
	- as the store passed register contents, the load must pass the register number for use later
![[Pasted image 20260425124604.png]]

- the above is the correct pipeline stage reflecting this
#### Graphically Representing Pipelines
- pipelines are hard to understand
	- many instructions are simultaneously executing in a single datapath in every clock cycle
- to aid understanding, there are two basic styles of pipeline figures
	- multiple-clock-cycle pipeline diagrams
	- single-clock-cycle pipeline diagrams
	- the multiple-clock-cycle diagrams are simpler, but do not contain all the details
- consider the following sequence:
```
lw    $10, 20($1)
sub   $11, $2, $3
add   $12, $3, $4
lw    $13, 24($1)
add   $14, $5, $6
```
- this is the multiple-clock-cycle diagram:
![[Pasted image 20260425125154.png]]
- time advances from left to right, and instructions advance from the top to the bottom
	- a representation of the pipeline stages is placed in each portion along the instruction axis,, occupying proper clock cycles
	- these datapaths represent the five stages of our pipeline graphically
- this is a more traditional representation of the same datapath
![[Pasted image 20260425125538.png]]
- this shows the actual physical resources being used at each stage:
![[Pasted image 20260425125620.png]]

- single-clock-cycle pipeline diagrams show the state of the entire datapath during a single clock cycle
	- all instructions in the pipelined are identified by labels above their respective pipeline stages
	- typically used to show the details of what is happening within the pipeline during each clock cycle
		- drawings appear in groups to show pipeline operation over a series of clock cycles
	- represents a vertical slice through a set of multiple-clock-cycle diagrams
- single-clock-cycle diagrams are more detailed and take significantly more space to show the same number of clock cycles

![[Pasted image 20260425125856.png]]
- this is a single clock cycle diagram corresponding to clock cycle 5 of the previous pipeline
### Pipelined Control
- just as we added control to the single-cycle datapath, we can also add control to the pipelined datapath
	- we borrow  the control lines from the existing datapath
		- same ALU control logic, branch logic, destination-register-number multiplexor, and control lines
	- as was the case for the single-cycle implementation, we assume the PC is written on each clock cycle
		- there is no separate write signal for the PC
			- similarly, there is no separate write signal for the pipeline registers
				- the pipeline registers are written during each clock cycle
- because each control line is associated with a component active in a single pipeline stage, control lines can be divided into five groups according to the pipeline stage
	- **Instruction fetch** 
		- the control signals to read instruction memory and to write the PC are always asserted
			- there is nothing special to control in this pipeline stage
	- **Instruction decode/register file read**
		- the same thing happens at every clock cycle
			- there are no optional control lines to set
	- **Execution/address calculation**
		- the signals to be set are RegDst, ALUOp, and ALUSrc
			- they select the Result register, the ALU operation and either Read data 2 or a sign-extended immediate for the ALU
	- **Memory Access**
		- the control lines set in this stage are Branch, MemRead, and MemWrite
			- branch equal, load, and store instructions set these signals
				- PCSrc select the next sequential address unless control assers Branch and the ALU result was 0
	- **Write-back**
		- the two control lines are MemtoReg, which decides between sending the ALU result or the memory value to the register file, and Reg-Write, which writes the chosen value
![[Pasted image 20260425131416.png]]


![[Pasted image 20260425131433.png]]

- pipelining the data path leaves the meaning of the control lines unchanged, we can use the same control values
![[Pasted image 20260425131705.png]]

- implementing control means setting the nine control lines to these values in each stage for each instruction
	- the simplest way to do this is to extend the pipeline registers to include control information
		- since control lines start with the EX stage, we can create control information during insturction decode