---
title: Software Architecture
sidebar: mydoc_sidebar_docs
permalink: klaytn_design/software_architecture.html
folder: mydoc
---

## 2-Layer Architecture Trust Model
Klaytn is based on a peer-to-peer network on the open Internet, with three tiers of nodes playing different roles in the system: consensus nodes, ranger nodes, and clients.

## Consensus Node (CN)
Consensus nodes (CNs) form the consensus network that is responsible for batching transactions into new blocks and confirming them using a Byzantine fault-tolerant (BFT) consensus algorithm. For each newly created block added to the chain, CNs are accountable for running smart contracts for the transactions sent to contract addresses. CNs communicate with each other via a broadcast protocol in which other nodes in the network never participate; the set of CNs can be changed only with the consensus of the existing CNs.

CNs form a private network called a CNN to run a BFT-based consensus algorithm. Klaytn requires CNs to apply BFT to a WAN (wide area network) and assumes that CNs are capable of providing sufficient hardware resources and network connections to meet these requirements.

### Block Generation

A 'round' is a block generation cycle in Klaytn. Each round generates a block, and after every N seconds, a new round starts.

#### Selecting Proposer and Committee

For each round, the platform randomly but deterministically selects a CN as a block proposer and a group of CNs as a committee. During the selection, the platform is not directly involved in the choice of either the block proposer or committee. Instead, each CN uses the same random number derived from the latest block header (*e.g.*, a secure hash of an ordered set of CN signatures — relatively unpredictable unless all signature providers collude) to run a cryptographic operation, which yields proof that it has (or has not) been selected. The committee size should be Byzantine resistant, and if the size of CNN is small, all nodes (except the proposer) are eligible for selection as committee members.

#### Block Proposal and Validation

Once selected, the block proposer broadcasts the proof of selection (i.e., a cryptographic token verifiable by the public key of the proposer) to all CNs. Then, only the committee members reply to the proposer with their selection proofs, notifying the proposer which committee members are needed to broadcast a new block.

A CN accepts transactions from clients and propagates them to the other CNs to form the global transaction pool. Once the proposer selects a set of transactions from the transaction pool and creates a block by ordering them, it runs a consensus algorithm with the committee to agree on the newly created block. Note that Klaytn can leverage any reliable BFT-based consensus algorithms as long as they are proven to be secure and meet the performance standard.

#### Block Propagation

A successful block must receive signatures from more than two-thirds of committee members. When the committee comes to a consensus, the newly created block with the required signatures will be propagated to all CNs, and the consensus round will end.

Once CNs generate blocks, RNs audit them via open validation. The goal of the open validation system is to promote transparency and deter adversaries from attacking or censoring the blockchain. RNs obtain blocks from CNs and audit the validity of obtained blocks. Verification is simple; for each block, RNs check whether the header contains more than two-thirds of the committee signatures and then examine transactions of the block by running them on their machines. To cooperate with this process, all CNs must post their public keys (after they are used to sign the blocks) in a publicly accessible place (e.g., block header). If an RN finds an insufficient number of signatures (i.e., Byzantine failure) in a block header or more than one block for the same block height (i.e., equivocation), then it alerts the others by gossiping through a public peer-to-peer network of RNs (RN network, or RNN).

The validated blocks are stored in the RN’s local storage to serve client requests. All blockchain read requests should go to RNs to effectively distribute requests across the RNs and allow CNs to focus on handling write requests. RNs, actively replicating the blockchain and serving client requests, will be rewarded with incentives. We will further discuss how open validation works and the incentive scheme that drives RNs to participate in the platform.


## Ranger Node (RN) & Open Validation
Ranger nodes (RNs) download newly created blocks from the CNs and gossip among themselves while storing a local copy of the blockchain. They validate the new blocks chosen by the CNs and check that the CNs never equivocate on the content of a given block height. Anybody can join the network as an RN.

### Open Validation and RN Duties

Open validation is essential in making Klaytn a public blockchain. Anyone can become an RN and validate the integrity of the platform by auditing blocks. Those RNs who actively contribute to the platform receive rewards in KLAY, and the platform makes its best effort to fairly distribute rewards. This section describes the auditing process and the RN duties.

An RN obtains blocks from one or more CNs. As described earlier, a valid block should have more than two-thirds of the committee signatures in its header, and no more than one block should be found at a block height. If an RN finds a block violating these conditions, it disseminates the information of the problematic block through the RNN. Hence, anyone who can reliably connect to the RNN and consistently audit the blocks can be an RN. We believe that a modern laptop with a high-speed Internet connection may be qualified for this role.

Becoming an RN is free; however, to earn the reward from the open validation, RNs must fulfill two responsibilities: *blockchain replication* and *serving client read requests*.

#### Blockchain Replication

Klaytn requires RNs to store audited blocks in their local storages, replicating the blockchain. The goal of this RN blockchain replication is to increase data redundancy to solve the resiliency problem of private blockchains. Generally, a blockchain with a higher replication factor is considered more secure, as it is resilient to compromises and censorship (i.e., tampering with data is difficult if many copies exist). Since the number of RNs participating in an RNN is not limited, data redundancy and the accompanying security are comparable to those of well-known public blockchains such as Bitcoin and Ethereum.

#### Serving Client Read Requests

The replicated blockchain data are then used not only to verify subsequent blocks but also to address client read (e.g., verifying transactions, retrieving states) requests. Traditional public blockchains have full nodes that address both read and write (e.g., fund transfer, state modification) requests. However, Klaytn delegates RNs to serve read requests, allowing clients to obtain blockchain information promptly from proximate RNs and enabling CNs to focus on addressing write requests. Considering that the size of a CNN is relatively small, delegating read requests to RNs alleviates CN resource constraints (e.g., network bandwidth).

Klaytn incentivizes RNs to compensate for the cost of storage and network bandwidth by periodically distributing tokens accumulated in the RN reward reserve. While different RNs make contributions of different sizes, the platform should make the best effort to distribute rewards equitably and proportional to the RN contribution to motivate RN participation. Thus, the platform requires RNs to submit a *proof of participation (PoP)* to determine the fair share of RN contributions.

### Proof of Participation
The PoP consists of two parts: proof of replication (PoR) and proof of serving clients (PoSC). RNs performs PoP to cryptographically prove that they have actively participated in the open validation and fulfilled their duties by honestly replicating blocks and actively serving client requests.

####	Proof of Replication
The PoR process is designed to show that an RN has indeed replicated the entire blockchain. Klaytn assumes that an active, honest RN maintains the entire blockchain, starting from the genesis block to the latest block, at any point in time. Hence, an active RN should be able to derive a cryptographic proof from a randomly chosen segment of blockchain data, verifiable by the CNs. A similar idea was proposed by Solana (also named the PoR) for the same purpose.

For each block at block height `h`, CNs include a random seed in the block header. Each RN uses the random seed to find the block segment, `seg`, an arbitrary byte sequence of the blockchain that the RN needs to verify. Once `seg` is found, an RN selects a small strip of `seg`, `s`, by randomly choosing a nonce, `x`, runs a PoW algorithm on `s` with `x`, and submits the result to a CN. We assume that global functions `C(h)`, which deterministically chooses a segment of the blockchain data for the given seed, and `B(seg, nonce)`, which also deterministically chooses `s` from `seg` for the given nonce `x`.

The number of KLAYs an RN receives depends on the effort the RN puts forth. For the CN-chosen difficulty, `d`, an RN needs to find a strip, `s`, such that `H(s, x) < d`, where `H` is a cryptographic hash function (e.g., SHA256) and `s = B(seg, x)` for a random nonce `x`. In our initial proposal, we define a tuple `(h, x)` as a share.

An RN who has successfully found a share submits it to a CN. CNs verify shares by reproducing the PoR (i.e., CNs compute the hash of the submitted strip which can be found by running `B(seg, x)` where `seg` is known as `C(h)`. If the computed hash satisfies the difficulty, then the share is admitted.

Verified shares will be recorded and rewarded periodically. CNs distribute KLAYs accumulated in the RN reward reserve to RNs based on the number of shares. That is, an RN receives KLAYs proportional to the number of successfully submitted shares. Note that there can be a lock-up period for the accumulated tokens.

####	Proof of Serving Client
Assuming that the ultimate goal of a rational RN is to maximize the reward, it is safe to say that most RNs will stay connected to CNs to keep up with the blocks to verify, submitting more shares, which will result in more rewards. PoR ensures the blockchain replication status of RNs, guaranteeing that they do not cheat and do contribute honestly to the network. Nonetheless, PoR does not force RNs to accommodate client requests. Without a proper reward mechanism, RNs may behave selfishly and refuse to meet client requests in their best interest.

To minimize these misbehaviors, Klaytn implements the PoSC to capture how well an RN has served requests by randomly monitoring RNs.

The PoSC is not a cryptographic proof but rather a process of verifying or ensuring the RN behavior when addressing client requests. The idea is straightforward: we want to ensure that an RN responds to client requests. Nonetheless, achieving this is not trivial, as too many variables exist. For instance, it is difficult to distinguish between a network failure and an intentional denial of service. Since it is challenging to examine or monitor all requests that go through RNs and determine their behaviors, we take a probabilistic approach with smart contracts.

For each block round, by using a technique similar to that used to determine block proposers and committee members, CNs randomly but deterministically choose RNs from the RNN and send arbitrary read requests (e.g., the latest block height). If RNs serve the requests, nothing happens; however, for those RNs failing to respond in time, CNs send penalty transactions to the RN reward reserve contract with the evidence of selection, incrementing the penalty count by 1. Consequently, RNs who failed to address client requests more than `k` times will be penalized by eliminating the rewards accumulated in the RN reserve reward. Once the rewards get slashed, the penalty count resets to 0.

Although the PoSC does not entirely prevent RNs from denying client requests, it motivates RNs to actively serve requests to maximize their profits, minimizing the likelihood of RN misbehavior.


## Servicechain
The throughput of a blockchain indicates the number of requests the platform can process per unit of time, and the sum of all requests handled by all DApps running on a specific platform cannot exceed its total throughput. As a result, many attempts have been made to improve blockchain throughput. However, in a platform in which multiple systems operate simultaneously (as opposed to one created for one purpose, such as Bitcoin), the required throughout increases exponentially.

In a blockchain platform, multiple services operate in parallel with each service requiring independent throughput. However, current blockchain platforms have a clear limitation on physical throughput. The performance of a platform that does not employ parallelization is bound to the performance of a single node or to the network bandwidth connecting the nodes. Klaytn is adopting the servicechain concept to solve the problem of throughput fundamentally and ensure a pleasant operating environment for users.

### Isolating Smart Contracts
The sum of all contracts and states created by the contract for the operation of one service is collectively referred to as the DApp, which functions as the minimum unit for the service operation. When executing a specific function of a service, multiple contract calls and data flow will occur within a DApp, without a connection to another DApp of an unrelated service.

Therefore, if DApps that are related to each other and likely to be referenced are isolated, the guarantee of the order of execution of the data flow or transactions can be limited to this isolated space, and the blockchain in which data are stored will be made available only in an isolated space, with the blockchain that stores the data being isolated using servicechains.

Within the DApp, which is isolated by the servicechain, users can freely call smart contract functions and share the token stake to acquire resources. Therefore, the convenience of management can be increased by placing the associated DApps on a servicechain for common use instead of requiring staking for an individual contract.

### Preserving Servicechain Integrity
The servicechain leaves data (i.e., block hashes) in a designated chain called a ‘regionchain’ that can validate the latest block data of the servicechains as a checkpoint and forms a loose ordering among servicechains via checkpoints. Several servicechains can run on a single physical node; hence, over hundreds of thousands of servicechains can be deployed on one node depending on the activity of the platform.

### Consensus of a Servicechain
Since most servicechains will not require much throughput per unit of time, it would be very inefficient to run a separate consensus round per servicechain, and the effect of grouping DApps into the servicechain would diminish.

Therefore, if the platform attempts to achieve consensus by collecting the transactions of multiple servicechains, an agreement can be reached without individual consensus processes.

Let us define the data obtained by a consensus algorithm as a ‘consensus container’ and establish that each container can include transactions created by multiple servicechains. A container can contain transactions of different servicechains, where a set of transactions originating from a servicechain is segregated from the other transactions. In a nutshell, each container has multiple segments, with each segment containing service-chain-specific transactions.

Hence, the node that governs a consensus process can remove the transactions of each servicechain from the transaction pool and insert them into the corresponding segment in the container to start a consensus process. The node participating in the consensus can check the validity of each segment by validating all transactions in a segment and then carry out a consensus process for those validated segments. If a segment contains invalid transactions, the node will mark the transaction as invalid and leave it in the proposed segment.

## Regionchain
Servicechains can be processed in parallel within a single node. Using this parallel execution can increase the throughput of a platform by effectively utilizing the computing resources of a single node; yet, it still cannot address the limitations of the performance of the platform due to resource capacity.

A set required for Klaytn operation consisting of CNs and RNs is called a region. At the beginning of Klaytn operation, the platform is composed of one region. As the platform is developed, additional regions will be created if the total amount of requests exceeds the throughput that the platform can handle.
