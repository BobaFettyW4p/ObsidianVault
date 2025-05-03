## Coordination and Synchronization
- **coordination**
	- the organization of the different elements of a complex body or activity so as to enable them to work together effectively
	- manage interactions and dependences between activities in a distributed system
- **synchronization**
	- the operation or activity of two or more things at the same time or rate or in a controlled sequence
	- process synchronization: ensure that one process waits for another to complete its operation

## Cristian's algorithm
- A requests the time from B
- after receiving the request, B prepares a response appends the time T from its own clock
- A sets the time to be `T+RTT/2`
	- assumption: time is symmmetric (this is ok on LAN)
![[Pasted image 20250503102613.png]]

## Network time protocol
- extends Cristian's algorithm for use on wide area, variable-latency networks
	- one of the oldest internet protocols still in use (1985)
- pairwise protocol between servers
	- A will probe B and B will probe A
- client polls *several* NTP servers and compute time offset and RTT delay
	- take pairs of values, discard outliers, keep the best candidates and adjust frequency gradually to fix any offset found

## NTP implementation
- issues:
	- symmetrical updates are not ideal if we know one server is more accurate than the other
	- what happens is we have loops?
- solution:
	- organize into a hierarchy where the number represents the distance from the reference clock (we only update from a higher stratum)
		- Stratum 0: high precision reference (atomic clock)
		- Stratum 1: primary time servers
			- computers synchronized within ms of stratum 0
		- Stratum 2: sync over network with S1 servers
		- ...
		- Stratum 16: wholly unsynchronized devices

## Precision over accuracy
- most clock sync algorithms rely on a passive time server
	- clients simply ask it for the time
- what do we do when machines don't have access to a time server or UTC clock?
	- in many cases it is *sufficient to have an agreement on time* rather than requiring UTC accurate time
- the **Berkeley algorithm** is decentralized, time daemons actively poll other machines for their time, compute the average, and tell other machines to advance/slow their clocks

![[Pasted image 20250503102831.png]]

- Many algorithms for synchronizing time
	- NTP/Cristian's algorithm syncs absolute time (accurate)
	- Berkeley algortithm for agreeing on some time (precise)

# Logical clocks
- in many cases, it is not necessary to have an accurate account of real time
	- we may just need every node to agree on a common time
	- but how often do we even need to know the relative times that events occurred?
- in many cases, we may only need to track the order of events
	- message `a` was sent before message `b`
- clock synchronization need not be absolute, if two processes do not interact it is not necessary that their clocks be synchronized

## The *happens before* relationship
- a -> b
	- if `a` and `b` are two events in the same process and `a` comes before `b`, then `a->b`
	- if `a` is the sending of the message and `b` is the receipt of the message, then `a->b`
- Transitivity
	- if `a-> b` and `b->c` then `a->c`
- Concurrency
	- if events happen in different processes that do not exchange messages then we have no idea of their relationship and nothing needs to happen

## Formalizing requirements
- how do we maintain a global view on the system's behavior that is consistent with the happened-before relation?
- attach a timestamp `C(e)` to each event `e`, satisfying the following:
	- P1: If `a` and `b` are two events in the same process, and `a->b`, then we require that `C(a) < C(b)`
	- P2: If `a` corresponds to sending message `m`, and `b` is the receipt of that message, then `C(a)<C(b)`
- Problem
	- how do we attach a time stamp to an event when there's no global clock?
	- maintain a consistent set of *logical clocks*, one per process 

### Solving these problems with Lamport's Logical Clock
- each process `Pi` maintains a local counter `Ci` and adjusts this counter:
	- each new event that takes place with `Pi`, `Ci` is incremented by 1
	- every time a message `m` is sent by process `Pi`, the message receives a timestamp
		- `ts(m) = Ci`
	- whenever a message `m` is received by a process `Pi`, `Pi` adjusts it's local counter `Ci`to `max{Cj, ts(m)};`, then executes step 1 before passing `m` to the application
- Notes: Property P1 is satisfied by 1, Property P2 by 2 and 3
- it can still occur that two events happen at the same time
	- solvable via PIDs

### Lamport's Logical Clock example
- we have these 3 processes that send messages between themselves as shown:
![[Pasted image 20250503104235.png]]
- their clocks are not synced as we can see
- `P1` sends `m1` to `P2`
	- since `m1` is sent at `t=6` and received at `t=16`, the happens before relationship is maintained and everything is fine
- similarly, `P2` sends `m2` to `P3` at `t=24` and it is received at `t=40` so there is no issues with the relationship, however....
![[Pasted image 20250503104444.png]]
- `m3` is sent at `t=60` and received at `t=56` and `m4` is sent at `t=64` and received at `t=54`
	- both of these messages are considered to have been received before they were sent, which violates P2
	- Lamport's logical clock fixes this by as so:
  ![[Pasted image 20250503104633.png]]
- since `m3` must be received after `m3` was sent, the clock on `P2` is revised so it is received one tick after it was sent, in this case `t=61`
![[Pasted image 20250503105126.png]]
- clock tick speed is kept consistent, so the tick immediately after the revised `t=61` tick is `t=69`
- in our example, this means `m4` is now considered to have been sent at `t=69`
	- since `m4` was received at `t=54`, we are still in violation of the happens before relationship
		- this is rectified by updating the clock on `P1` to have been considered to have received `m4` at `t=70`, like so:
![[Pasted image 20250503105345.png]]

### What else can we infer from logical clocks?
- Lamport's logical clock provide guarantees on the ordering of events
	- if `a` happened before `b`, then `C(a) < C(b)`
- they do not say anything about the relationship between two events by comparing their time values
	- `C(a) < C(b)` does not imply that `a` happened before `b` in the absence of other correlation between `a` and `b`

### Causality: concurrent message transmission
- we know the ordering of messages `Mi < Mj`
	- we know when messages are received, but may not be able to conclude anything about their relationship
![[Pasted image 20250503105621.png]]
- in this example, we know `m1` is received at `t=16` and `m2` is received at `t=24`, and so we know `m1` is received before `m2`
	- you might believe that since `m1` was sent at `t=6` and `m2` was sent at `t=20`, that you can conclude that `m1` was sent before `m2`
		- this is incorrect, and it's incredibly hard to determine this conclusively in a distributed system
			- luckily, we rarely have to care about this
				- what we often care about is *causality*

## Vector clocks
- each `P` maintains a vector `VCi`
	- `VCi [i]` is the logical clock at process `Pi`
	- if `VCi [j] = k`, then `Pi` knows that `k` events have occurred at `Pj`
- maintaining vector clocks
	- before executing an event `Pi` executes `VCi [i] <- VCi [i] + 1`
	- When process `Pi` sends a message `m` to `Pj`, it sets `m`'s vector timestamp `ts(m)` equal to `VCi` after having executed step 1
	- Upon the receipt of a message `m`, process `Pj` sets `VCj [k] <- max{VCj [k], ts(m)[k]}` for each `k`, after which is executes step 1 and delivers the message to the application

### Vector clock example

![[Pasted image 20250503110714.png]]
- in this example:
- `m1` initiates on process `P2` and is sent to `P1`
	- the vector clock on `P2` is incremented when the message is sent, the vector clock for `P1` increments when it is received
- `m1` is then sent from `P1` to `P3`
	- the vector clock on `P1` is incremented when the message is sent, and the vector clock for `P3` is incremented when it is received
- next, `P1` performs some processing
	- the vector clock for `P1` is incremented for this
- `m3` is sent from `P1` to `P2`
	- `P1` vector clock is incremented when it is sent, and `P2`s is incremented when it's received
- finally `m4` is sent from `P2` to `P3`
	- the vector clock for `P2` is incremented when it is sent, `P3`'s when it is received
- in this instance, `m2` may causally precede `m4`
	- we cannot determine for sure, however
		- even though `m3` is sent from `P1` after `m2`
		- and `m3` is received before `m4` is sent, we cannot say 100% sure that `m2` is received before `m4`, but it's pretty likely

![[Pasted image 20250503111657.png]]

- in this example, processes `m2` and `m4` can conflict
	- we can't be 100% sure which one occurs first with a vector clock
		- would need to rely on a logical clock to do so

![[Pasted image 20250503111812.png]]
### Summary
- clock synchronization need not be absolute
	- if two processes do not interact, then their clocks don't have to be synchronized
- Logical clocks allow us to update a counter and guarantee that events occur in some order (i.e. a before b)
	- we cannot say anything about *causality* using counters
- vector clocks are lightweight Lamport logical clocks that can determine potential causality and conflict *after the fact*

# Mutual exclusion
- how do we control simultaneous access to the same resource?
	- avoid corruptions, inconsistency, etc
- fundamental problem associated with concurrency and collaboration amongst processes
- solution: grant *mutually exclusive* access to a process (i.e. mutex)
	- same idea as mutual exclusion on a multi-processor/thread system

## two main approaches
- permission-based approach
	- process wanting to access a resource first acquires permission from other processes
- token-based approach
	- pass a special message ("token") between processes
	- only one available
		- whoever has it can access the shared resource

### Simple approach: centralization
- the easiest approach is to simulate the same model as in a single processor system
	- elect one process as the *coordinator*
- must ask the coordinator when you want access to a resource
	- coordinator will only grant access if no other process is accessing the resource
![[Pasted image 20250503112444.png]]
- Process `P1` asks the coordinator for permission to access the resource. Permission is granted by the coordinator
- Process `P2` requests access while `P1` is still using it. The coordinator does not reply
- Once `P1` releases the resource, it does so by informing the coordinator
	- the coordinator replies to `P2` and grants them access
#### Centralized coordinator, pros and cons
- Pros
	- easy to demonstrate that it guarantees mutual exclusion
	- fair
		- requests are granted in the order they arrive
	- no starvation
		- no process waits forever
	- easy to implement
	- relatively efficient
		- 3 messages per resource
- cons
	- single point of failure
	- processes are not able to distinguish a dead coordinator from permission denied

### Distributed algorithm
- when a process wants to access a resource, it builds a message containing `<resource name, process number, current logical time>`
- when a process receives a request from another process it has 3 different options
	- if the receiver is not (and does not want to) access the resource -> send OK to sender
	- if the receiver already has access to the resource, it does not reply - > Queue request
	- if the receiver wants to access the resource but has not yet done so, it compares the timestamp of the incoming message with the one contained in the message that it has sent everyone
		- lowest one wins
			- if the incoming message has a lower timestamp -> send ok
			- if its own message has a lower timestamp, queue the incoming request and send nothing
#### What happens if two processes try to access at the same time?
![[Pasted image 20250503113240.png]]
-  two processes, `0` and `2` want to access a shared resource at the same time
![[Pasted image 20250503113251.png]]
- in this case, `P0` has a lower timestamp, so it wins and is granted access to the resource
![[Pasted image 20250503113337.png]]
- when `P0` is finished, it grants access to `P2`
#### Pros and Cons
- pros
	- mutual exclusion guaranteed
	- no starvation or deadlock
- cons
	- messaging overhead: `(N-1)` to request access, `(N-1)` OKs
	- `N` points of failure
		- requires a response from each other process
			- overcome by altering the algorithm
				- when a request arrives, the receiver always replies
					- either grant/deny
					- can also add a timeout to detect if request is lost and to retry
						- or to conclude that it is dead
	- requires multicast communication
		- and that each process is aware of all other processes

### Token ring algorithm
- organize processes in a logical ring, and let a token be passed between them
	- Token circulates around the ring from `Pk` to `Pk+1`
- process that currently holds the token is allowed to enter the critical region (if it wants)
![[Pasted image 20250503113633.png]]
#### Pros and Cons
- Pros
	- guarantees mutual exclusion
	- starvation cannot occur
	- detecting failures isn't so bad
		- when passing the token on, a process can determine if it's neighbor failed
- Cons
	- when a process wants to access the resource, it might have to wait for every other process
	- if the token is lost, it has to be regeneration
		- even detecting the token is lost may be hard as there is no guaranteed time for the token to circulate
### Decentralized mutual exclusion
- fully decentralized approaches can be constructed using a voting algorithm
- requires a majority vote from `m > N/2` coordinators to access the resource
- if a coordinator does not want to give permission, it will tell the requestor that it cannot vote for them
#### Pros and Cons
- Pros
	- without failures, mutual exclusion cannot be guaranteed
	- no starvation of deadlock
- Cons
	- issues is processes fail (or if they forget their votes)
		- how do we know? how do we guarantee that the quorum can be updated?
	- the correctness of the voting mechanism can be violated if there is only a minority of non faulty coordinators
	- in practice the probability of violating correctness is low
	- efficiency can be low if many processes are competing to access the same resource
		- many successive voting rounds in a row without a process being granted access to the resource
##### Summary
- We often need to manage concurrent access to shared resources
- Grant *mutually exclusive* access to a processs
- **Centralized** - simple but not fault tolerant
- **Distributed** - fault tolerant but inefficient
- **Token ring** - simple but can be slow to access
- **Decentralized** - flexible but complicated to implement

## Election algorithms
- many distributed algorithms require one process to act as a coordinator, initiator or another role
	- mutual exclusion algorithms, time servers, consensus
- it doesn't matter which process, but we must guarantee there is one
- Assumptions:
	- all processes have a unique ID
	- all processes know the IDs of all other processes in the system
	- processes do not know if another process is *up* or *down*
	- our aim is to elect one of these processes
		- simplified as choosing the highest ID that is up
- **Bully** - send election messages to all processes with higher identifiers, if no one replies, the sender wins the election and becomes coordinator
- **Ring** - send election messages in a ring, after the message goes around the entire ring, the one with the highest ID becomes coordinator

### Bully algorithm
- consider `N` processes `{P0, ... Pn-1}` where `id(Pk) = k`
- when process `Pk` notices the coordinator is no longer responds to requests, it initiates an election
	- `Pk` sends an ELECTION message to all processes with higher identifiers
		- `Pk+1, Pk+2, ..., Pn-1`
	- if `Pk` does not receive a response, it will consider itself the winner of the election and become coordinator
	- if a process with higher ID answers, it will take over
- the winning process sends a COORDINATOR message to all other process
- if a process that was down comes back up, it holds an election
	- if it's the highest, it will win the election and take over

#### Bully algorithm example
![[Pasted image 20250503124715.png]]
- in the above example, 7 was the coordinator but went down
![[Pasted image 20250503124741.png]]
- 4 is the first node that detects the coordinator is not active and initiates an election
![[Pasted image 20250503124825.png]]
- 5 and 6 both respond to 4's election request and take over the election process from 4, 4 will not win the election and is not selected to the be the next coordinator
![[Pasted image 20250503124913.png]]
- 5 and 6 will then conduct an election, and since 7 is down, 6 will win
![[Pasted image 20250503124939.png]]
- in response, 5 will concede defeat and stop holding an election, leaving 6 as the coordinator
![[Pasted image 20250503125004.png]]

## Ring algorithm
- process priority is obtained by organizing processes into a logical ring and the process with the highest priority should be elected as coordinator
	- any process can start an election by sending an election message to its successor
		- if a successor is down, it goes to the next successor
	- if the message is passed on, the sender adds itself to the list
		- when it gets back to the initiator, every up node in the ring has made its presence known
	- the initiator sends a coordinator message around the ring containing a list of all living processes
		- the one with the highest priority wins the election and becomes the coordinator
![[Pasted image 20250503125758.png]]
- in this example, node 6 and 3 both started an election to elect a successor simultaneously
	- both nodes send a message to their successor in the ring containing their IDs
		- the successors append their names to the message with their IDs and send the message around the ring
			- once the originators of the messages receive the message back, they can conclude that every node in the ring was able to append their ID to the list in the message
				- the message will then be sent around the ring again
					- if a node receives the message and their ID is the highest, they will win the election and assume coordinator duties
