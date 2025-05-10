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
## Dependable systems
- **availability**
	- system is ready to be used immediately
		- the probability that a system is operating correctly at any given moment
		- expressed in terms of 9s
- **reliability**
	- how long can you run continuously without failure? defined in terms of a time interval
		- 1 hour, 1 day, 1 week
- **safety**
	- ensure no catastrophic event happens if the system temporarily fails
		- important for planes and power plants
- **maintainability**
	- ease by which a failed system can be repaired
		- mean time to resolution

## Anatomy of failure
- a system *fails* when it cannot meet its promises
- an *error* is the part of a system's state that may lead to failure
- the cause of an error is a *fault*
	- transient faults occur once and disappear
	- intermittent faults occur, then vanish, then reappear
	- permanent fault continues to exist until the faulty component is replaced
		- software bug, hardware fault
- *fault tolerance* means that a system can provide services in the presence of faults

### Failures in more detail
- Crash failure: halts, but is working correctly until it halts
- Omission failure: fails to respond to incoming requests
	- Receive omission: fails to respond to incoming messages
	- Send omission: fails to send messages
- Timing failure: response lies outside a specified time interval
- Response failure: response is incorrect
	- Value failure: the value of the response is wrong
	- State-transition failure: Deviates from the correct flow of control (request structure)
- Byzantine/arbitrary failure: may produce arbitrary responses at arbitrary times
	- accidental or malicious

### Faults in synchronous and asynchronous systems
- *synchronous system*: process execution speeds and message delivery times are bounded
	- when a server shows no more activity when it is expected to do so, a client can conclude that it has crashed
- *asynchronous system*: no assumptions about process execution speeds or message delivery times are made
	- when a client no longer sees any actions from a server it cannot conclude that the server has crashed
		- it may be slow or its messages may have been lost

### Partially synchronous systems
- pure synchronous systems exist only in theory
- stating that every distributed system is asynchronous is not what we see in practice
	- overly pessimistic
- realistic to assume a system is *partially synchronous*; most of the time it behaves as a synchronous system, but there is no bound on the time that it behaves in an asynchronous fashion
	- asynchronous behavior is an exception
		- we can normally use timeouts to conclude that a process has indeed crashed
			- occasionally this conclusion is false
- in practice, we have to design fault-tolerant systems that can withstand incorrectly detecting that a process halted
### Failures in partially synchronous systems
- **fail-stop failure**: when a system detects its own failure and shuts down immediately
	- crash failures that can be reliably detected
	- may occur when assuming non-faulty communication links and when the failure-detecting process `P` can place a  worst-case delay on responses from `Q`
- **fail-noisy failures**: almost like a fail stop, but are not detected immediately
	- `P` will eventually come to the correct conclusion that `Q` has crashed
		- there may be some unknown time in which `P`'s detection of the behavior of `Q` are unreliable
- **fail-silent failures**: we assume that communication links are non-faulty, but that process cannot distinguish crash failures from omission failures
- **fail-safe failure**: cover the case of dealing with arbitrary failures, yet they are benign; they do no harm
- **fail-arbitrary failures**: may fail in any possible way; failures may be un-observable in addition to being harmful

### Masking failures
- the best we can do is try and hide failures from other processes
- Information redundancy
	- add extra bits to recover from garbled bits
- time redundancy
	- action performed, and if needed it is performed again
- physical redundancy
	- extra equipment or processes added to tolerate loss of component
	- could be done at the hardware or software level

### Resilience
- what we would like to do is:
	- detect failures
	- mask failures
	- recover from failures
- *resilience* is the ability to provide an acceptable level of service in the presence of failures

### Resilience by process groups
- key approach is to organize several identical processes into a group
	- when a message is sent to a group, all members receive it
	- if one fails, another can take over
- groups may be dynamic, processes can join/leave, processes may be members of different groups at the same time
- we need mechanisms to organize and manage process groups
#### group organization
- flat group
	- all processes are equal and decisions are made collectively
	- symmetrical/no single point of failure
	- decision-making is complicated and decisions can take time
- hierarchical group
	- one process is the coordinator and all others are workers
	- loss of coordinator means we need to find a replacement
	- decisions can be made quickly
![[Pasted image 20250504100611.png]]
##### how does this help us
- groups allow us to mask faulty processes in the group
	- we replicate processes and organize into a group
- need to consider how much replication is needed
	- a system is said to be *k-fault tolerant* if it can survive faults in `k` components
	- with silent failures, then having `k+1` processes is sufficient
	- with byzantine failures we need at least `2k+1` non-faulty processes
		- if k sends the wrong answer, then you need `k+1` to send the correct answer
			- "believe the majority"

## Consensus
- process by which nodes in a distributed system reach agreement on something

### Background: important system properties
- safety
	- something bad will never happen
	- consensus: no nodes will decide on different values
- liveness
	- something good will happen (eventually)
		- no specification on the time bound
		- system will make progress despite concurrent execution
	- consensus: all nodes will eventually decide on a value

### How do we achieve consensus?
- agreement of many nodes on shared state, i.e. a data value
- general model
	- *proposes* a value by suggesting to others
	- *decides* based on what it thinks everyone agrees on
### Fischer-Lynch-Patterson (FLP Result)
- one of the most important results in distributed systems
- consensus was known to be solvable in synchronous systems
- FLP: consensus cannot be guaranteed in an asynchronous distributed system within bounded time if a node *might* fail
	- we cannot reliably detect failure between a crashed node and a slow node
- no asynchronous consensus algorithm can guarantee both safety and liveness

### 2 phase commit
- **phase 1**: commit-request (or voting)
	- coordinator suggests value to all nodes and gathers responses (yes/no)
	- participants execute transaction up to commit
- **phase 2: commit**
	- if everyone agrees, coordinator tells nodes the value is final, they commit, then ACK
	- if anyone disagrees, tell all nodes to rollback, then ACK
- does this solve the consensus problem?
	- what happens if a node crashes?
		- participant? coordinator?
![[Pasted image 20250504101133.png]]

### 3 phase commit
- **phase 1**: voting
	- identical to 2 phase system
- **phase 2**: prepare commit
	- if all reply yes then coordinator sends "prepare commit"
	- each node executes up to commit and ACKs receipt of messages
- **phase 3**: commit
	- if coordinator receives ACK from all nodes it tells all nodes to commit
	- if any node says no it aborts
- this solves the issues with the two phase commits and its inability to recover from failure, but there are other issues
![[Pasted image 20250504101220.png]]

# Paxos

## Basic protocol
- **1a: Prepare**
	- a *proposer* selects a proposal number `n` and sends a *prepare* request with number `n` to a majority of *acceptors*
		- `n` is greater than any number used by a proposer
- **1b: Promise**
	- if an *acceptor* receives a *prepare* request with number `n` greater than that of any *prepare* request to which it has already responded, then it responds to the request with a *promise* not to accept any more proposals numbered less than `n` and with the highest-numbered proposal that it has accepted
	- otherwise, the *acceptor* can ignore the proposal
- **2a: Accept**
	- if the *proposer* receives a promise response to its *prepare* requests from a majority of acceptors, then it sends an *accept* request to those *acceptors* with a value `v`, where `v` is the value of the highest-numbered proposal among the responses
		- `v` is any value if the responses reported no proposals
- **2b: Accepted**
	- If an *acceptor* receives an *accept* request for proposal `n`, it accepts the proposal unless it has already responded to a *prepare* request having a number greater than `n`
		- otherwise it ignores it
	- an *acceptor* will send an accept message to the *proposer* and every *learner*

### Paxos example

![[Pasted image 20250504102446.png]]
- here is a series of nodes united under Paxos
![[Pasted image 20250504102613.png]]
- Node 3 wants to write a value `v=1` to the ledger in place `p=15`...
![[Pasted image 20250504102728.png]]
- so it sends a prepare request to the other nodes.
	- nodes 1 and 2 now check their ledgers to ensure they do not have a value of `n` greater than 15 in their ledgers
![[Pasted image 20250504103014.png]]
- if this is so, they will promise not to accept another proposal
	- these nodes will then send a promise response to the sending node if they have not
	- if they have, they will reply with that value, and 3 would to send a new prepare request with a higher value of `n`
![[Pasted image 20250504103115.png]]
- once the proposer receives an promise message from a majority of acceptors, it is clear to add the value to ledger at the proposed value
	- will send an accept message to all acceptors
![[Pasted image 20250504103340.png]]
- when the acceptors receive the accept message, they will append the value `v` to their ledger at location `n`

### Paxos example with failed node

![[Pasted image 20250504103904.png]]
- we are using the same system as before, with the same proposed values for `n` and `v`
![[Pasted image 20250504103935.png]]
- node 3 sends the prepare request as in the above example
![[Pasted image 20250504104125.png]]
- and the acceptors send their promise requests like you would expect
![[Pasted image 20250504104339.png]]
- but then the proposer node fails
- 
![[Pasted image 20250504104405.png]]
- since the acceptors have already sent a promise message, they will not accept an alternate value for any position `n` in the ledger smaller than the value they have already promised
![[Pasted image 20250504104730.png]]
- if a greater value is sent, there is a Paxos safety mechanism in place that will update incomplete prior commits before processing a new one
![[Pasted image 20250504104739.png]]
- the value of the prior incomplete commit will be appended to the new place in the ledger, ensuring the value is written
- it would then be possible to add `v=5` to the ledger at position `n=18` or greater
### failure modes
- failure of an acceptor (with quorum alive)
	- no recovery needed (no additional messages required)
- failure of a learner
	- no recovery needed (no additional messages required)
- failure of proposer without conflict
	- what we just saw
- failure of proposer with conflict
	- e.g. when the leader recovers
	- can lead to many unsuccessful rounds
		- will lead to stalling of the procol

- Paxos guarantees safety not liveness

### Paxos Summary
- guarantees safety (consistency)
	- can withstand `f` failures with `2f+1` acceptors
- liveness?
	- No, but conditions for preventing progress are rare
- difficult to implement
- many variants and optimizations
- any node can commit a value in 2RTT
	- in multi Paxos, the leader can do it in 1RTT
- Paxos + replicated state machine == fault tolerant services

# Raft
## An understandable consensus algorithm
- an effort to make decision decisions based on understandability
	- strong leader model
- problem decomposition
- minimize state space
	- multiple problems with single mechanism
	- no special cases
	- minimize nondeterminism

### Problem decomposition: 3 core problems
- leader election
	- elect one server to act as leader
		- all message passing initialized by leader
			- detect crashes, choose new leader
- log replication
	- leader accepts commands from clients, appends to its log
		- clients must communicate with leader
	- leader replicates its log to other servers
		- overwrite inconsistencies
- safety
	- keep logs consistent
	- only servers with up to date logs can become leader
### Terms
- logical clock model
	- each node has its own local view of time
		- "current term"
	- current term increases monotonically over time
- Every Raft communication includes and compares current term
	- 1 leader per term
	- some terms have no leader (failed election)
- terms are updated when a node starts an election or when the current term communicated from another server is higher than its own
- communication with a node with a higher term is rejected
	- if a candidate learns of a higher term it returns to being a follower
![[Pasted image 20250504105221.png]]

![[Pasted image 20250504105248.png]]

### Leader election
- a leader is responsible for sending heartbeats to every other node
	- if a node does not get a heartbeat from the leader within the timeout period, it initializes an election
- follower increments current term and transitions to candidate state
- votes for itself
- issues *RequestVote* to each other node
- waits until:
	- the node wins the election
	- another server establishes itself as the leader
	- a period of time passes without a winner
		- election will be restartd in this case
- other nodes will vote for at most one winner

### Log replication
- leader accepts commands from clients
	- commands to other nodes are directed to the leader
- appends commands to its own log
- issues *AppendEntries* RPC to each other node
- when command is applied to all other nodes, leader then commits it to their state machine
	- includes all prior entries
- if followers crash or run slowly, the leader retries indefinitely until all followers store all log entries

### Leader failure
- the leader handles inconsistencies by forcing the followers logs to duplicate its own
	- strong leader model
- find the latest log where the two logs agree, delete entries in the follower's log after that, and send follower the leader's entries
- safety guaranteed by several restrictions:
	- election restriction: voting process to prevent candidate from winning unless its log contains all committed entries
	- committing from previous term: if leader crashes before commit, future leaders will attempt to complete the commit
## Raft leader election

![[Pasted image 20250504105555.png]]
- assume a Raft system with no leader
- each node in the system is waiting for a heartbeat from the leader for a random period of time
![[Pasted image 20250504105640.png]]
- eventually, one of these nodes' waiting period will elapse, and they will start an election to elect a new leader
	- in our case, node 2
- they vote for themselves, and send a RequestVote message to each other node
![[Pasted image 20250504105921.png]]
- once all the votes are collected, a candidate that receives a majority of votes becomes the leader
![[Pasted image 20250504110004.png]]
- this denotes the start of a new term
- the leader will then capture logs from all nodes in the grouping, and will enforce this log to be replicated across all nodes
## Raft setting a value
- here we have a Raft system with node 2 serving as the leader
![[Pasted image 20250504110136.png]]
- when the leader appends a value to its log, it must enforce this on all nodes in the consensus group
- it will send a message to all the nodes requesting to set the log to a specific value
![[Pasted image 20250504110328.png]]
- and they will update their logs accordingly, and respond back to the leader that they are prepared to update their logs
![[Pasted image 20250504110420.png]]
- the leader will then set the local log to the agreed upon value and reach out to all other nodes to update the value

## Dealing with failure in Raft
- imagine a Raft system where node 2 is the leader, but it fails
![[Pasted image 20250504110510.png]]
- eventually, one of the nodes (in this case node 1) will determine the leader is no longer responding to messages and initiate an election
![[Pasted image 20250504110541.png]]
- eventually, the node will win the election in the process previously specified
![[Pasted image 20250504110609.png]]
- and resume normal operation
![[Pasted image 20250504110621.png]]

### Challenges with Raft
- split vote
	- if the vote is split, no node becomes leader
	- if this happens, wait for the next timeout and election
		- most implementations issue random heartbeat timeouts for exactly this reason
- network partitions
	- not a real concern as we need a quorum to update state

#### Raft Summary
- understandable distributed consensus algorithm
	- few states, simple transition conditions, simple rules
- addresses the replicated state machine problem
- strong leader model
	- several pros and cons
- safety
	- leader election/log commitment rules guarantee safety
- liveness
	- competing candidates could cause a repeated split vote
		- random timeouts mitigate this, but it's rare in practice
- not designed to address Byzantine failures

## Basic idea
- to keep replicas consistent, we need to ensure that all *conflicting operations* are done in the *same order everywhere*
- Conflicting operations:
	- read-write conflict: a read operation and a write operation act concurrently
	- write-write conflict: two concurrent write operations
- guaranteeing global ordering on conflicting operations may be a costly operation (and reduce scalability)
	- solution: weaken consistency requirements so that hopefully global synchronization can be avoided

### Consistency model
- a *consistency model* is a contract between processes and the data store
	- it says that if processes (or the data store) agree to obey certain rules, the store promises to work correctly
- normally, a process that performs a read operation on a data item expects the operation to return a value that shows the results of the last write operation on that data
- without a global clock, it's hard to know which operation was the last.... we use more relaxed consistency models restricting what can be returned
#### Sequentially consistent?
![[Pasted image 20250510081326.png]]
- Since `W(x)a` on `P1` and `W(x)b` on `P2` have no direct correlation, we cannot determine definitively which starts and completes first (without additional information)
- Since `P3` and `P4` both read the value of `x` to be `b` and then later read it to be `a`, this is considered to be sequentially consistent
![[Pasted image 20250510081924.png]]
- conversely this system is not sequentially consistent
	- `P3` sees `W(x)b` completing before `W(x)a`
	- `P4` sees `W(x)a` completing before `W(x)b`
	- thus, the writes were considered to have completed in different orders on different processes and are not sequentially consistent


### Causal consistency
- remember we often don't care about sequential ordering, what we care about is ordering of events that are *causally related*
	- if event `a` is caused by event `b`, causality means everyone should see `b` first and then see `a`
- for a data store to be considered causally consistent, it is necessary the store obeys the following conditions:
	- *writes that are potentially causally related must be seen by all processes in the same order*
	- *concurrent writes may be seen in a different order on different machines*

![[Pasted image 20250510082713.png]]
- In this system, `P1` creates a causal relationship between `W(x)a` and `W(x)c`
	- namely, that `W(x)a` occurs before `W(x)c`
- similarly, `P2` establishes a relationship between `W(x)a` and `W(x)b`
	- that `W(x)a` occurs before `W(x)c`
- a system is causally consistent is all causal relationships are respected
	- on `P3`, both of these relationships are respected
		- `R(x)a` comes before `R(x)c`
		- `R(x)a` comes before `R(x)b`
	- same for `P4`
	- in this case, the system is not sequentially consistent because the same sequential order is not represented across all processes
		- i.e. on `P3` `R(x)c` comes before `R(x)b`, but on `P4`, `R(x)b` comes before `R(x)c`
![[Pasted image 20250510084309.png]]
- this system is not causally consistent
	- `P2` establishes a causal relationship between `a` and `b`
		- namely, `a`->`b`
			- this relationship is not seen on `P3`, thus making the system not causally consistent

### Eventual consistency
- concurrency is not always common
	- e.g. a database typically has many more reads than writes
	- e.g. on the web, pages are typically updated by one person and users typically tolerate cached data (proxies/browsers)
- **in the absence of write-write conflicts**, the state will eventually converge across replicas
- great for building performant systems

	- **sequential consistency** - all processes see the same order of all operations
	- **causal consistency** - writes that are potentially causally related must be seen by all processes in the same order. concurrent writes may be seen in a different order on different machines
	- **eventual consistency** - in the absence of write-write conflicts, all replicas will eventually converge toward identical copies of each other

## From data to client-centric consistency
- data-centric consistency aims to provide system-wide consistency across a data store
	- this is hard and we may be able to relax consistency requirements
- we consider a type of data store where we don't have many concurrent updates
	- when updates do happen, they can be easily resolved as they don't affect one another
		- mostly read-centric workloads
- we can apply various weak consistency models (e.g. eventual consistency) from the perspective of the client
![[Pasted image 20250510085246.png]]

### Goal
- eventual consistency guarantee is
	- "given no updates, all clients will see exactly the same state of a system in sometime"
- if we stop doing writes, the system will (eventually) converge to some consistent state
	- this is a very weak constraint that is probably simpler to implement and could be very performant
		- not always possible
#### Monotonic reads
>if a process reads the value of a data item x, any successive read operation on x by that process will always return that same or a more recent value
- the read operations performed by a single process `P` at two different local copies of the same data store
![[Pasted image 20250510090729.png]]
- why example b is not a monotonic-read:
	- After P$_1$ has read x$_1$, it later reads x$_2$
	- the previous write W$_2$(x$_1$|x$_2$) produces an x$_2$ that is independent from x$_1$
		- thus, P$_1$'s read at L$_2$ is known not to include the effect of its own write when it reads at R$_1$ 
#### Monotonic writes
> a write operation by a process on a data item x is completed before any successive write operation on x by the same process
- if we have two successive operations W$_k$(x$_i$)` and `W$_k$(x$_j$) by process P$_k$, then regardless of where W$_k$(x$_j$) takes place, we also have W(x$_i$;x$_j$)
![[Pasted image 20250510091854.png]]
- example `b` is not monotonic because:
- P$_2$ produces a concurrent version to x$_1$ after it produces x$_3$ but concurrently to x$_1$

#### Read your writes
> the effect of a write operation by a process on data item x will always be seen by a successive read operation on `x` by the same process
![[Pasted image 20250510092321.png]]
- in example `b`, P$_2$ produces a version concurrently to x$_1$
	- the effects of the previous write operation by P$_1$ have not necessarily propagated to L$_2$ when x$_2$ was produced
		- when P$_1$ reads x$_2$, it will not see the effects of its own write operation at L$_1$

#### Writes follow reads
> a write operation by a process on a data item x following a previous read operation on x by the same process is guaranteed to take place on the same or a more recent value of x that was read
![[Pasted image 20250510092530.png]]
- example `b` does not follow writes follow reads because:
	- P$_3$ produces a version x$_2$ concurrently to that of x$_1$
		- when P$_2$ updates x after reading x$_1$, it will be updating a version it has not read before
			- x$_2$ squashed x$_1$
### Replica management
- important to think about where, when and by whom replicas should be placed
- two related problems:
	- *where* should we place replica servers?
	- *how* should we place content on replicas?

### Choosing replica locations
- figure out the best `K` places out of `N` possible locations
	- select best location out of `N` for which the average distance to clients is minimal
		- then choose the next best location
			- computationally expensive
	- select the K$^{th}$ largest autonomous system (set of nodes **managed by the same organization**) and place a server at the best connected host
		- computationally expensive
	- position nodes in a `d`-dimensional geometric space, where distance reflects latency
		- identify the `K` regions with highest density and place a server in each
- not so much a concern these days as we have many cloud datacenters to easily place replicas

### Server-initiated replicas
- replicas are created when needed to improve performance
	- typically created by the owner/data store
- ex. web server replicas
	- a server that receives a burst of requests far from the server might want to deploy replicas in regions close to the requests
	- a heavily loaded server might add additional instances to reduce load
- each server keeps track of access counts per file, aggregated by considering server closest to requesting clients
	- number of accesses drops below deletion threshold D -> drop file
	- number of accesses exceeds replica threshold R -> replicate file
	- number of access between D and R the file may only be migrated
![[Pasted image 20250510093503.png]]
### Client initiated replicas
- commonly known as *caching*
	- local storage used for temporarily storing a copy of requested data
	- improve access to data that is *likely to be used again*
	- could be on the local machine or could be a nearby machine
		- institution cache
	- most often the server has no knowledge of the cache, and provides no influence on caching decisions
- big question is how long to keep data in a cache (when to invalidate)
	- either because data is stale or to make room for other data
- many approaches: LRU, FIFO, size-based
#### State vs operations
- what should be propagated
	- notification of update
		- just that the data has changed
		- common with cache invalidation models
		- uses little bandwidth, good when many more updates than reads
	- transfer data from one copy to another
		- most common approach
		- good when many more reads than writes
		- must update before the next read happens
	- send the update operation to other copies
		- each replica managed by process that keeps data up to date
		- minimal bandwidth, but more work on replica

#### Push vs pull
- **pushing updates**: server-initiated approach, in which update is propagated regardless whether target asked for it
	- efficient when many more reads than writes, i.e. we expect the replica to be read
- **pulling updates**: client-initiated approach, in which the client requests to be updated
	- efficient when many more writes than reads
![[Pasted image 20250510093945.png]]

##### Hybrid approach: leases
- dynamically switch between pulling and pushing using *leases*
	- a contract in which the server promises to push updates to the client until the lease expires
- make lease expiration time dependent on system's behavior
	- adaptive leases
	- **age-based leases**: an object that hasn't changed for a long time, will not change in the near future, so provide a long-lasting lease
	- **renewal-frequency based leases**: the more often a client requests a specific object, the longer the expiration time for that client will be
	- **state-based leases**: the more loaded a server is, the shorter expiration times become

#### Remote write protocols
![[Pasted image 20250510094127.png]]
#### Local-write protocols
![[Pasted image 20250510094142.png]]
#### Replicated write protocols
- write operations carried out by multiple replicas at the same time
	- active replication: operation forwarded to all replicas
	- consistency protocols: majority voting
- read and write quorums
![[Pasted image 20250510094202.png]]
