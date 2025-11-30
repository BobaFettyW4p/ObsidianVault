
---
title: Merge Sort
type: concept
course: algorithms
week: 1
topic: divide and conquer
tags:
  - algorithms
  - divide-and-conquer
  - week1
  - concept
related:
  - [[week1-divide_and_conquer/pseudocode/Merge Sort|Merge Sort pseudocode]]
  - [[week1-divide_and_conquer/concepts/Recurrences|Recurrences]]
  - [[week1-divide_and_conquer/concepts/Master Theorem|Master Theorem]]
  - [[week1-divide_and_conquer/Week1 Dashboard|Week1 Dashboard]]
---

- applies the divide and conquer approaches to sorting

![[Pasted image 20251128183817.png]]

![[Pasted image 20251128183828.png]]

- *Input:* one array $A[n]$ of size $n$

### Overview
```mermaid
flowchart TD
	A["A[0..n-1]"]
	
	A --> L["L"]
	A --> R["R"]
	
	L --> SL["sort(L)"]
	R --> SR["sort(R)"]
	
	SL --> M["merge(L,R)"]
	SR --> M
	
	M --> OUT["Sorted A"]
```

### Example

```mermaid
flowchart TD
    A["5 2 7 4 3 1 2 6"]

    A --> L1["5 2 7 4"]
    A --> R1["3 1 2 6"]

    L1 --> L2["5 2"]
    L1 --> R2["7 4"]

    R1 --> L3["3 1"]
    R1 --> R3["2 6"]

    L2 --> A1["5"]
    L2 --> A2["2"]

    R2 --> A3["7"]
    R2 --> A4["4"]

    L3 --> A5["3"]
    L3 --> A6["1"]

    R3 --> A7["2"]
    R3 --> A8["6"]

    A1 --> M1["merge"]
    A2 --> M1
    M1 --> S1["2 5"]

    A3 --> M2["merge"]
    A4 --> M2
    M2 --> S2["4 7"]

    A5 --> M3["merge"]
    A6 --> M3
    M3 --> S3["1 3"]

    A7 --> M4["merge"]
    A8 --> M4
    M4 --> S4["2 6"]

    S1 --> M5["merge"]
    S2 --> M5
    M5 --> Lsorted["2 4 5 7"]

    S3 --> M6["merge"]
    S4 --> M6
    M6 --> Rsorted["1 2 3 6"]

    Lsorted --> M7["merge"]
    Rsorted --> M7
    M7 --> OUT["1 2 2 3 4 5 6 7"]
```

