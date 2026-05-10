- data hazards may occur when one instruction requires as input the result of a previous instruction
```
add $so, $t0, $t1 // add the value in t1 to t0 and store the result in s0
sub $t2, $s0, $t3 // add the value in t3 to s0 and store the result in t2
```
- will $\texttt{s0}$ be ready in time?
	- if the add is an atomic operation, yes, otherwise no