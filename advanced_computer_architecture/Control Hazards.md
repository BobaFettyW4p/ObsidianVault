- occur at branch statements where the result is not known until it is executed
	- wtihout knowing whether a branch is taken, the next instruction is unknown, resulting in a stall
- ex:
```
add $t4, $t5, $t6
beq $t1, $t2, addr // if $tt2=$t1, branch to the instruction stored in address addr
lw $t3, 300($t0) // load the value stored at specified address into register t3
```

- $\texttt{lw}$ instruction can't be executed until branch is evaluated, which takes several cycles
	- can stall until evaluated
		- impact: at least one cycle, depending on the branch type
	- predict: guess one directon, then back up if wrong
		- impact: 0 lost cycles if correct, 1 if wrong
			- more dynamic schemes will predict based on the history of 1 branch
	- delayed branch - redefine branch behavior, reorder instructions to run other instructions while waiting for the branch to evaluate
		- impact: 0 lost cycles per branch instruction if another instruction can be found to put in the slot
- ex:
```
add $s0, $t0, $t1 // add the value in register t1 to the value in register t0 and store the result in register s0
sub $t2, $s0, $t3 // add the value in register t3 to the value in register s0 and store the result in register t2
```

- will s0 be ready in time?