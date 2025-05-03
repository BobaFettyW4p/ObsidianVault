## Introduction
- distrivuted systems rely on processes being able to cooperate to achieve a given goal
	- **problem**: how do we *coordinate* and *synchronize* these processes?
- Examples:
	- manage shared access to resouces so processes are not accessing at the same time
	- agree on the order of 

## Coordination and Synchronization
- **coordination**
	- the organization of the different elements of a complex body or activity so as to enable them to work together effectively
	- manage interactions and dependences between activities in a distributed system
- **synchronization**
	- the operation or activity of two or more things at the same time or rate or in a controlled sequence
	- process synchronization: ensure that one process waits for another to complete its operation

## Time
- in a centralized system *time* is easy
	- single clock, everyone trusts that clock blindly
- process wants to know the time... it asks the OS
	- if process A gets the time then `process B` gets the timv, Tb > Ta
		- we know which event happened first
- think about all the places we implicitly rely on knowledge of time:
	- file timestamps, documentation synchronization, Github, compiling code, SSH connections
- **RECALL** - distributed systems have no global clock

## Clock
- physical clocks in computers are timers based on oscillation frequency of a crystal
- clock *tick* - interrupt occurring when **counter** hits 0 (ticks normally happen many times per second)
- clock *skew* - the difference between clocks
- clock *drift* - the rate that a clock moves relative to real time
	- quartz-based hardware clock can drift ~32 seconds per year
- goals within a distributed system
	- internal synchronization: keep deviation between two clocks on any two machines within a specified bound (*precision*)
	- external synchronization: keep bound to an external reference source suc as UTC (*accuracy*)

## How do we find out the time
- someone has a more accurate idea of time (e.g. GPS or Atomic clock)
	- ask them to send us the time!
- dlays are inconsistent, mesages are lost, etc.

## Synchronization algorithm
- Cristian's algorithm
- NTP - built atop Cristian's algorithm
- Berkeley algorithm

## Cristian's algorithm
- A requests the time from B
- after receiving the request, B prepares a response appends the time T from its own clock
- A sets the time to be `T+RTT/2`
	- assumption: time is symmmetric (this is ok on LAN)

## Fixing incorrect time
- when you discover time is out of sync, what can you do?
- can we go back in time?
	- often not possible
- better to slow down until correction has been made

## Network time protocol
- extends Cristian's algorithm for use on wide area, variable-latency networks
	- one of the oldest internet protocols still in use (1985)
- pairwise protocol between serevers
	- A will probe B and B will probe A
- client polls *several* NTP servers and compute time offset and RTT delay

## NTP implementaiton
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

### Summary
- synchronization and coordination is a crucial aspect of computer systems and distributed systems
	- the idea of doing the right thing at the right time
- distributed systems make it hard as there is no globally shared clock and processes on different machines have their own notion of time
	- even with the synchronization algorithms we discussed, we cannot know the exact order of events with any certainty
- Many algorithms for synchronizing time

# Logical clocks
- in many cases, it is not necessary to have an accurate account of real time
	- we may just need every node to agree on a common time
	- but how often do we even need to know the relative times that events occurred?
- in many cases, we may only need to track the order of events
	- message `a` was sent before message `b`
- clock synchronization need not be absolute, if two processes do not interact it is not necessary that their clocks be synchronized

## The happens before relationship
- a -> b
	- if a and b are two events in the same process and a comes before b, then a->b
	- if a is the sending of the message and b is the receipt of the message, then a->b
- Transitivity
	- if a-> b and b->c then a->c
- concurrency
	- if events happen in different processes that do not exchange messages then we have no idea of their relationship and nothing needs to happen
## Formalizing requirements
- how do we maintain a global view on the system's behavior that is consistent with the happened-before relation?
- attach a timestamp `C(e)` to each event `e`, satisfying the following:
	- P1: If a and b are two events in teh same process, and a->b, then we require that C(a) < C(b)
	- P2: If a corresponds to sending message m, and b is the receipt of that message, then C(a)<C(b)
- Problem
	- how do we attach a time stamp to an event when there's no global clock?
	- maintain a consisten set of logical 

### Solving these problems with Lamport's Logical Clock
- each process Pi maintains a local counter Ci and adjusts this counter:
	- each new event that takes place imcrements its counter by 1
	- every time a message m is sent by process p, the message receives a timestamp
	- whenever a message is received by a process, P adjusts it's local counter to max, then executes step 1 before passing to the application

### What else can we infer from logical clocks?
- provide guarantees on the ordering of events
	- if a happened before b, then C(a) < C(b)
- they do not say anything about the relationship between two events by comparing their time values
	- C(a) < C(b) does not imply that a happened before b
### Causality: concurrent message transmission
- we know the ordering of messages Mi < Mj
	- we know when messages are received, but 
- what we often care about is *causality*

## Capturing causality
- tracing causality is simple if we assign each event a unique name (e.g. process ID + local counter)
	- Pk is the kth event that happened at process P
- the problem becomes how do we track causal histories
	- e..g if two events happened at P, then the causal history H(p2) of event 2 is {p1,p2}
- Now if we send a message from P to Q:
	- this is an event P3 on P and a new event Qk on Q(e.g. q2 if it already has {q1})
	- P also needs to send its causal history to Q
	- and Q needs to merge these causal histories into a new history
### Problem with causal histories
- representation is not very efficient
	- store and share all events
- in practice, we don't need all events, just the last one
- assign an index to each process and represent the causal history as a vector

## Vector clocks
- each P maintains a vector VCi
- maintianing vector clocks
### Summary
- clock synchronization need not be absolute
	- if two processes do not interact, then their clocks don't have to be synchronized
- Logical clocks allow us to update a counter and guarantee that events occur in some order (i.e. a before b)
	- we cannot say anything about *causality* using counters

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
## simple approach: centralization
- the easiest approach is to simulate the same model as in a single processor system
	- elect one process as the *coordinator*
- must ask coordinator when you want access to a resource
	- coordinator will only grant access if no other process is accessing the resource

### Pros and cons
- Pros:
	- easy to demonstrate that it guarantees mutual exclusion
	- fair
	- no starvation
	- easy to implement
	- relatively efficient
- cons:
	- single point of failure
	- can't distinguish a dead coordinator from permission denied (no message is returned)
### Distributed algorithm