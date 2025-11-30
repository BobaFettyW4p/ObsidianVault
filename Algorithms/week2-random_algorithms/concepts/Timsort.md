---
title: Timsort
type: concept
course: algorithms
week: 2
topic: random algorithms
tags:
  - algorithms
  - random-algorithms
  - week2
  - concept
related:
  - [[week2-random_algorithms/concepts/Quicksort|Quicksort (Randomized Perspective)]]
  - [[week2-random_algorithms/concepts/Randomized Sorting Algorithms|Randomized Sorting Algorithms]]
  - [[week2-random_algorithms/Week2 Dashboard|Week2 Dashboard]]
---

- randomized quicksort is a big improvement, but carries some limitations
	- not good with duplicates
		- one options to resolve is to add a 3rd array for values equal to the pivot
			- this modified quicksort will run in linear time if there are a lot of duplicates in the input array
- Timsort is a hybrid algorithm that combines merge sort and insertion sort
- compute $min-run(A)$
	- divide $A$ into subarrays that are contiguous of size $min-run$
		- perform insertion sort on each subarray
