---
layout: post
title:  "Mastering Blockchain - Blockchain 101"
date:   2021-07-05 10:21:22 -0700
categories: blockchain
---

# Questions

The questions that I'd like to find out when reading this chapter are
- What is Blockchain?
- When did it get introduced?
- What are blockchain applications and why do we need to care about blockchain?


# The introduction of Bitcoin

* Bitcoin was introduced in 2008 with a promise to impact every industry.
* It might take years before the practical benefits come to fruition.
* Proofs of concept using blockchain technology.
* The groundbreaking paper [Bitcoin: A Peer-to-Peer Electronic Cash System](https://bitcoin.org/bitcoin.pdf) under the pseudonym
Satoshi Nakamoto and introduced the term `chain of blocks`.

# Distributed System

- a computing paradigm whereby two or more nodes work with each other in coordinated fashion
  in order to achieve a common outcome.

- the main challenge in distributed system is coordination between nodes and fault tolerance.

- some nodes break, the network should continue to work flawlessly.

# CAP Theorem

- CAP Theorem states that any distributed system cannot have Consistency, Availability, and Partition Tolerance simultaneously.

- Consistency: all nodes in a distributed system have a single latest copy of data.
- Availability: system is up.
- Partition Tolerance: if a group of nodes fails the distributed system still continues to operate correctly.

But Blockchain can -- really?

- Fault Tolerance is achieved using replication.
- Consistency is achieved using consensus algorithms - state machine replication.

- Practical Byzantine Fault Tolerance - to solve the Byzantine Generals problem.

# Consensus Mechanism

1. Agreement -- all honest nodes decide on the same value
2. Termination -- all honest nodes terminate execution of the consensus process and eventually reach a decision.
3. Validity -- the value agreed upon by all honest nodes must be the same as the initial value proposed by at least one honest node.
4. Fault Tolerance -- the consensus algorithm should be able to run in the presence of faulty or malicious nodes.
5. Integrity - this is a requirement where by no node makes a decision more than once.

# Types of Consensus Mechanism

- Byzantine fault tolerance-based: no intensive compute operations.  this method relies on a simple scheme of nodes that publish signed messages.
- Leader-based consensus mechanisms: This type of mechanism requires nodes to compete for the leader election lottery
    and the node that wins it proposes a final value.


# Blockchain
- Blockchain was introduced in 2008 with the invention of bitcoin.
- Electronic cash is the first application of blockchain.

##### The concept of Electronic cash
- Accountability and anonymity.
- Hashcash - POW system to control email spam.  Simple idea -- legitimate users want to send emails then they are required to
compute a hash as a proof that they have spent a reasonable amount of computing resources before sending the email.

# Questions

- What is a Byzantine node?
- What are some consensus algorithm?
