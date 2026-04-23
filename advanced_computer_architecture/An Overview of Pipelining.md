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
- all pipeline stages take a singel cycle