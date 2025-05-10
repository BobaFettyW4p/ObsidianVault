## Background: faults
- faults are inevitable in any systems
	- node failure
	- network failure
	- software failure
	- power failure
	- data center failure
	- client failure

### Failures are event created intentionally
- Netflix's chaos monkey
## Faults in distributed systems
- unlike centralized systems, distributed systems may often have partial failures:
	- part of the system fails while the remaining part continues to operate
- ideally we want to automatically discover/recover from partial failures without affecting performance

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

### Detecting failures
- how can we reliably detect that a process has crashed?
	- either actively send "are you alive?" or passively wait for messages
- General model
	- each process is equipped with a failure detection module
	- A process probes another process for a reaction
	- If it reacts, it is considered to be alive
	- if the process does not react within a specified time period: it is suspected to have crashed

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
##### summary
- failures should be expected: hardware, network, software, power
- distributed systems may often have partial failures
- resilience means that we are able to detect, mask, and recover from partial failures without affecting performance
	- process groups provide redundancy

# Distributed consensus
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

### Background: replicated state machine model
- general model for implementing fault-tolerant services by replicating servers
- Lamport: replicating a service over multiple nodes is easy if you present the same sequence of input commands to all replicas and they follow the same succession of states
![[Pasted image 20250504100806.png]]
### How do we achieve consensus?
- agreement of many nodes on shared state, i.e. a data value
- general model
	- *proposes* a value by suggesting to others
	- *decides* based on what it thinks everyone agrees on
- relied on by:
	- distributed databases, group membership systems, replicated state machines, data stores, message queues, anywhere we need mutual exclusivity...
	- underlies most distributed systems: banks, cloud providers, online video services, retailers, any distributed system with fault tolerance
### Two generals problem
- hard to coordinate disparate nodes to achieve consensus on a result without a bound of time
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
## The part time parliament
- parliament to determine the law of the land
	- represented by a sequence of decrees
- no member is willing to remain in Chamber to record actions
	- each legislator maintains their own leger to record decrees
- no two ledgers can have a different entry for that decree
	- trivially met by leaving ledgers blank, need some way to guarantee progress
- legislators are willing to pass any decree that is proposed
	- issue: if one group passes a decree and then another group enters and passes a conflicting decree
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
		- will lead to stalling of the protocol

- Paxos guarantees safety not liveness


### Paxos adds two things over 2PC
- Paxos adds two important mechanisms to 2PC
	- ordering the proposals so it may be determined which of two should be accepted
	- consider a proposal accepted when a majority of acceptors have indicated that they

#### Paxos in practice
- nobody likes it

### Summary
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

![[Pasted image 20250504105245.png]]

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

### Is Raft byzantine fault tolerant?
- No
	- flaw of the strong leader model
	- there are extensions that make it

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