- simple pipelines demonstrate the power of pipelining, but don't demonstrate what happesn with real programs
	- all calculations are independent, none of them use the results from prior calculations
		- leads to a data hazard
			- obstacle to pipelined execution
![[screenshots/Pasted image 20260425161658.png]]

- consider the following sequence with many dependencies:
```
sub    $2, $1, #3    # Register $2 written by sub
and    $12, $2, $5   # 1st operand($2) depends on sub
or     $13, $6, $2   # 2nd oeprand ($2) depends on sub
add    $14, $2, $2   # 1st($2) and 2nd($2) depend on sub
sw     $15, 100($2)  # Base ($2) depends on sub
```

- the last 4 instructions are all dependent on the result in register $\texttt{\$2}$ of the first instruction
	- if register $\texttt{\$2}$ had the value $10$ before the subtract instruction and $-20$ afterwards, the programmer intends that $-20$ will be used in the following instructions that refer to register $\texttt{\$2}$
- this diagram shows how these instructions would operate in the pipeline:
![[screenshots/Pasted image 20260425162034.png]]
- the top shows the value of register $\texttt{\$2}$ as the pipeline proceeds
	- it changes during pipe cycle 5, when the $\texttt{sub}$ instruction writes its result
- the last potential hazard can be resolved by the design of the register file hardware
	- what happens when a register is read and written in the same clock cycle?
		- we can assume the write is in the first half of the clock cycle and the read is in the second half
			- the read delivers what was written
			- when done this way, there is no data hazard
- this shows that the values read for register $\texttt{\$2}$ would not be the result of the $\texttt{sub}$ instruction unless the read occurred during clock cycle 5 or later
	- the instructions that receive the correct value of $-20$ are the $\texttt{add}$ and $\texttt{sw}$
		- the $\texttt{and}$ and $\texttt{or}$ instructions get the incorrect value of 10
- the desired result is available at the end of the $\texttt{EX}$ stage (clock cycle 3)
	- the data is actually needed by the $\texttt{and}$ and $\texttt{or}$ instructions at the beginning of the $\texttt{EX}$ stage in clock cycles 4 and 5 respectively
- a notation that names the fields of the pipeline registers allows for a more precision notation of dependencies
	- e.g. "ID/EX.RegisterRS" refers tot he number of one register whose value is found in the pipeline register ID/EX
		- the one from the first read port of the register file
		- the first part of the name is the name of the pipeline register
		- the second part is the anme of the field in that register
		- this gives us the following pairs of hazard conditions:
			- EX/MEM.RegisterRd = ID/EX.RegisterRs
			- EX/MEM.RegisterRd = ID/EX.RegisterRt
			- MEM/WB.RegisterRd = ID/EX.RegisterRs
			- MEM/WB.RegisterRd = ID/EX.RegisterRt

##### Dependence Detection
>Classify the dependencies in this sequence:

```
sub    $2, $1, #3    # Register $2 written by sub
and    $12, $2, $5   # 1st operand($2) depends on sub
or     $13, $6, $2   # 2nd operand ($2) depends on sub
add    $14, $2, $2   # 1st($2) and 2nd($2) depend on sub
sw     $15, 100($2)  # Base ($2) depends on sub
```
- the $\texttt{sub-and}$ is a type 1a hazard
- the $\texttt{sub-or}$ is a type 2b hazard:
	- MEM/WB.RegisterRd = ID/EX.RegisterRt = $\texttt{\$2}$
- the two dependencies on $\texttt{sub-add}$ are not hazards because the register file supplies the proper data during the ID stage of $\texttt{add}$
- there is no data hazard between $\texttt{sub}$ and $\texttt{sw}$ because $\texttt{sw}$ reads $\texttt{\$2}$ the clock cycle after $\texttt{sub}$ writes $\texttt{\$2}$

- because some instructions do not write registers, this policy is inaccurate: sometimes it would forward when it shouldn't
	- one solution is to check if the RegWrite signal will be active
		- examining the WB control field determines whether it will be asserted
- once we are able to detect hazards, we still have to forward the proper data
![[screenshots/Pasted image 20260425170612.png]]
- this diagram shows the dependence between pipeline registers and the inputs to the ALU
	- the dependence begins from a pipeline register, as opposed to waiting for the WB stage to write the register file
		- the required data exists in time for later instructions
			- the pipeline registers hold the data to be forwarded
![[screenshots/Pasted image 20260425171710.png]]

![[screenshots/Pasted image 20260425171726.png]]
- the EX/MEM.RegisterRd field is the register destination for an ALU instruction or a load
![[screenshots/Pasted image 20260425180853.png]]

- forwarding can also help with hazards when store instructions are dependent on other instructions
	- if they use just one data value during MEM, forwarding is easy
	- however, loads immediately followed by stores can require additional forwarding hardware to make them run faster
		- due to frequent copies
### Data Hazards and Stalls
- one case where forwarding cannot save the day is when an instruction tries to read a register following a load instruction that writes the same register
![[screenshots/Pasted image 20260425183510.png]]
- consider the above
	- the data is still being read from memory in clock cycle 4 while the ALU is performing the operation for the following instruction
		- something has to stall the pipeline for the combination of load followed by an instruction that reads its result
- in addition to a forwarding unit, we need a *hazard detection unit*
	- the hazard detection unit operates during the ID stage so it can insert the stall between load and use