- what is the chance that two people in this room have the same birthday?
- how do we carry out an experiment to find the answer?
- the chance $n+1$ people don't have the same birthday:
$$
1 - \frac{365}{365} \times \frac{364}{365} \times \frac{363}{365} \times \dots \times \frac{365-n}{365}
$$
- often easier to count the complement to what you want to compute
	- chance $n$ people do not share a birthday
- many problems do not have simple analytical solutions
	- these require simulation

```
for each experiment m
	for each student n
		select random number 1...365
		
if match
	counter++

probability = counter/m
```

- under what conditions will the experimental result converge to the true expected value?
- how can a computer generate random numbers?
- what is the rate of convergence?
- how can this be efficiently implemented on a digital computer?
- what hardware and programming model abstractions are apprppriate?
- can I use the tool to solve my problem well enough?