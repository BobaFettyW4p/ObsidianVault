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
- crash failure: halts, but is working correctly until it halts
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
- get the definitions from the slides later

### Partially synchronous systems
- pure synchronous systems exist only in theory
- stating that every distributed system is asynchronous is not what we see in practice
	- overly pessimistic
- realistic to assume a system is partially synchronous; most of the time it behaves as a synchronous system, but there is no bound on the time that it behaves in an asynchronous fashion

### Failures in partially synchronous systems
- fail-stop failure: when a system detects its own failure and shuts down immediately
- fail-noisy failures: almost like a fail stop, but are not detected immediately
	- some amount of time you're expecting a result but don't receive it
- fail-silent failures: we assume that communication links are non-faulty, but that process cannot distringuish crash failures from omission failures
- fail-safe failure: cover the case of dealing with arbitrary failures, yet they are benign; they do no harm
- fail-arbitrary failures: may fail in any possible way; failrues may be un-observable in addition to being harmful

### Detecting failures
- how can we reliably detect that a process has crashed?
	- either actively send "are you alive?" or passively wait for messages
- General model
	- each process is equipped with a failure detection module
	- A process probes another process for a reaction
	- If it reacts, it is considered to be alive
	- if it does not react within a specified time period: it is suspected to have crashed

### Masking failures
- the best we can do is try and hide failures from other processes
- Information redundancy
	- add extra bits to recover from garbled bits
- time redundancy
	- action performed, and if needed it is performed again
- physical redundancy
	- extra equipment or processes added to tolerate loss of component
	- could be done at the hardware or software level

### resilience
- what we would like to do is:
	- detect failures
	- mask failures
	- recover from failures
- *resilience* is the ability to provide an acceptable level of service in the presence of failures

### resilience by process groups
- key approach is to organize several identical processes into a group
	- when a message is sent to a group, all members receive it
	- if one fails, another can take over
- groups may be dynamic, processses can join/leave,m processes may be members of different groups at the same time
- we need mechanisms to organize and manage process groups

#### group organization
- flat group
	- all processes are equla and decisions are made collectively
	- symmetrical/no single point of failure
	- decision-making is complicated and decisions can take time
- hierarchical group
	- one process is the coordinator and all others are workers
	- loss of coordinator means we need to find a replacement
	- decisions can be made quickly
##### how does this help us
- groups allow us to mask faulty processes in the group
	- we replicate processes and organize into a group
- need to consider how much replication is needed
	- a system is said to be k-fault tolerant if it can survive faults in k compoennts
	- with silent failures, then having k+1 processes is sufficient
	- with byzantine failures we need at least 2k+1 non-faulty processes
		- if k sends the wrong answer, then you need k+1 to send the correct answer
			- "believe the majority"
##### summary
- failures should be expected: hardware, network, software, power
- distributed systems may often have partial failures
- resilience means that we are able to detect, mask, and recover from partial failures without affecting performance
	- process groups provide redundancy

# distributed consensus
## Consensus
- process by which nodes in a distributed system reach agreement on something

### background: important system properties
- safety
	- something bad will never happen
	- consensus: no nodes will decide on different values
- liveness
	- something good will happen (eventually)
		- no specification on the time bound
		- system will make progress despite concurrent execution
	- consensus: all nodes will eventually decide on a value

### background: replicated state machine model
- general model for impelmenting fault-tolerant services by replicating servers
- lamport: replicating a service over multiple nodes is easy if you present the same sequence of input commands to all replicas and they follow the same succession of states
### How do we achieve consensus?
- agreement of many nodes on shared state, i.e. a data value
- general model
	- proposes a value by suggesting to others
	- decides based on what it thinks everyone agrees on
- relied on by:
	- distributed databases, group membership systems, replicated state machines, data stores, message queues, anywhere we need mutual exclusivity...
	- underlies most distributed systems: banks, cloud providers, online video services, retailers, any distributed system with fault tolerance
### two generals problem

### Fischer-Lynch-Patterson (FLP Result)
- one of the most important results in distributed systems
- consensus was known to be solvable in synchronous systems
- FLP: consensus cannot be guaranteed in an asynchronous distributed system within bounded time if a node *might* fail
	- we cannot reliably detect failure between a crashed node and a slow node
- no asynchronous consensus algorithm can guarantee both safety and liveness

### 2 phase commit
- phase 1: commit-request (or voting)
	- coordinator suggests value to all nodes and gathers responses (yes/no)
	- participants execute transaction up to commit
- phase 2: commit
	- if everyone agrees, coordinator tells nodes the value is final, they commit, then ACK
	- if anyone disagrees, tell all nodes to rollback, then ACK
- does this solve the consensus problem?
	- what happens if a node crashes?
		- participant? coordinator?

### 3 phase commit
- phase 1 voting
- phase 2 prepare commit
	- if all reply yes then coordinator sends "prepare commit"
	- each node executes up to commit and ACKs receipt of messages
- phase 3 commit
	- if coordinator receives ACK from all nodes it tells all nodes to commit
	- if any node says no it aborts

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
## basic protocol
- 1a prepare
	- a proposer selects a proposal number n and sends a prepare request with number n to a majority of acceptors
- 1b promise
	- if an acceptor receives a prepare request with numb n greater than that of the prepare request, then it responds to the request with a promise to accept any more proposals numbered less than n and with the highest-numbered proposal that it has accepted
	- otherwise, the acceptor can ignore the proposal
- 2a accept
	- if the proposer receives a promise response to its prepare requests from a majority of acceptors, tehn it sends an accept request to those acceptors with a value v, where v is the value of the highest-numbered proposal among the responses
		- any value is responses reported no proposals
- 2b accepted
### failure modes
- failure of an acceptor (with quorum alive)
	- no recovery needed (no additional messages required)
- failure of a learner
	- no recovery needed (no additional messages required)
- failure of proposer without conflict
	- what we just saw
- failure of proposer with conflict

- Paxos guarantees safety not liveness

### Paxos adds two things over 2PC
- Paxos adds two important mechanisms to 2PC
	- ordering the proposals so it may be determined which of two should be accepted
	- consider a proposal accepted when a majority of acceptors have indicated that they

#### Paxos in practice
- nobody likes it

### Summary
- guarantees safety (consistency)
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
			- detect crashes, choose new leadr
- log replication
	- leader accepts commands from clients, appends to its log
		- clients must communiate with leader
	- leader replicates its log to other servers
		- overwrite inconsistensies
- safety
	- keep logs consistent
	- only servers with up to date logs can become leader

### terms
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

### leader election
- a leader is responsible for sending heartbeats to every other node
	- if a node does not get a heartbeat from the leader within the timeout period, it initializes an election
- follower increments current term and transitions to cnadidate state
- votes for itself
- issues RequestVote to each other node
- waits until:
	- wins the election
	- another server establishes itself as the leader
	- a period of time passes without a winner
- other nodes will vote for at most one winner

### log replication
- leader accepts commands from clients
	- commands to other ndoes are directed to the leader
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
	- voting process to prevent candidate from winning unless its log contains all committed entries
	- if leader crashes before commit, future leaders will attempt to complete the commit
