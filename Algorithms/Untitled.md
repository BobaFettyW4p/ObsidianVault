office hours

square roots, b:
start with an array from 1..n
divide and conquer
then check ceil n^2, and if it matches, return

square roots, c:
start with an array from 1..n
binary search
check ceil n^2, and if it matches, return

- anything that can be done recursively can be done iteratively, and vice versa

- one way to analyze asymptotic runtime is to calculate the recurrence
	- more general than master theorem
	- you can solve it by expanding
T(n) = T(n-1) + O(1)

- always default to writing the runtime recurrence
- 