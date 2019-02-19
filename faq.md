## Why do we use BFT?

Byzantine fault-tolerance (BFT) consensus algorithm has long been studied to address failures in synchronized distributed computing systems. By design, a system or a network that can prevent Byzantine failure can reach an agreement if at least two-thirds of nodes are not malicious. 

Recall that a blockchain is a network of nodes agreeing on the history of blocks; nodes in a blockchain can be faulty or (deliberately) malicious and cause Byzantine failure to delay or falsify blocks. BFT in the blockchain, thus, resolves risks of failed consensus, preventing unreliable nodes from hindering the block generation.

Using BFT is advantageous over PoW-based consensus in cases where the number of nodes is fixed and small; it has better throughput and does not waste energy to solve cryptographic puzzles. Although it is not the most efficient algorithm allowing multiple nodes to agree on one value, it is clear that BFT is one of the practical approaches to ensure the order of transactions among many nodes connected via a synchronous network without sacrificing much performance.

## What if there are many CNs?

Since Klaytn uses a BFT-based consensus algorithm, having many nodes in consensus is disadvantageous. Studies show having more than 16 nodes running PBFT delays consensus significantly, hindering block generation.

However, it is our goal to increase the number of CNs for decentralizing data and trust further, possibly to hundreds of nodes. We address this problem by randomly but verifiably selecting a subset of CNs for each consensus round. A known technique such as verifiable random function (VRF) enables us to choose a random subset of nodes while proving that the selection is indeed correct.

By limiting the number of CNs per consensus round (e.g., between 7 to 16), the platform can perform consensus swiftly while giving fair chances to all participating CNs. As a result, Klaytn keeps the network decentralized while retaining performance improvement promised by the BFT algorithm.

## How many RNs do we need?
The more the better. RNs are supposed to decentralize data and trust. The role of RN nodes is to validate the integrity of the platform by auditing blocks. Assuming that the data security and resiliency are derived attributes obtained from the size of a blockchain network, Klaytn needs sufficiently many RNs to achieve comparable security to Bitcoin and Ethereum. 

Considering the replication factor of Ethereum in early 2018 is around 15,000 (i.e., the sum of the number of miners and full nodes), we believe Klaytn can be recognized as decentralized and secure as Ethereum with 15,000 or more RNs.

## What are the differences between Klaytn and Ethereum?

Klaytn runs similar to Ethereum except for the consensus algorithm. It even keeps compatibility with Ethereum Byzantium in RPC/API interfaces and executes smart contracts written in Solidity. In a sense, one may refer to Klaytn as a faster version of Ethereum. However, such an effort of making compatibility with Ethereum is meant to help developers who are used to Ethereum join Klaytn with less friction, enabling their soft-landing on a new blockchain platform.

We plan to provide much more than Ethereum; the additions that Klaytn offers to clients include (but not limited) to various execution environments for smart contracts written in traditional programming languages and enterprise-friendly features enabling companies to integrate business intelligence and security systems. The primary goal of Klaytn is to provide a blockchain platform that is usable for enterprises and blockchain-based applications. Ultimately, what makes Klaytn truly different from Ethereum is the way we find and offer essential features of the blockchain for those applications trying to disrupt the traditional market using blockchain technologies.

There are a few clear differences between Klaytn and Ethereum.

1. **Affordable execution cost**
One of the reasons that blockchains charge fees on smart contract executions is mostly to prevent various attacks from outside. As a result, Ethereum decided to intentionally increase the financial cost of running smart contracts to prevent any form of attacks. However, it can also dampen ordinary smart contract executions due to high gas prices on opcodes. To encourage people to use smart contract with an affordable fee, Klaytn uses a different opcode-based fee model with low unit cost per opcode and a step-wise pricing policy.

2. **High Performance**
A widely used approach to estimate the performance of a public blockchain is measuring the transactions per second (TPS). As of May 2018, the performance of Bitcoin was 7 TPS, while that of its direct competitor, Ethereum, was 25 TPS. It is hard to expect their service to be widely used when you consider the average TPS of VisaNet is approximately 2,000 (designed to handle up to 56,000). Klaytn aims to offer much more efficient and faster blockchain platform by having fewer nodes and deploying the network to relatively closer nodes.

3. **Co-governed by Klaytn contributors**
We all know that the ideal governance model for a blockchain platform is the one that allows all the participants of the network to involve and enables a swift decision-making process for the benefit of the platform. But the reality is, ordinary users of a blockchain have neither enough interest to be involved in a decision-making process nor the knowledge to make a right decision. Thus, Klaytn believes that platform contributors should be the entity taking governance since their interests are precisely aligned with that of the platform. In other words, platform contributors will take a serious look before making any decisions and there is a high possibility they would make beneficial decisions for all of us including themselves.



## What is EP?

EP stands for enterprise proxy. And this feature differentiates Klaytn from other blockchain platforms. EP is made to satisfy the business requirements of enterprise blockchain users and service providers while still containing the essential quality of public blockchain. 

The EP is basically an off-chain proxy communicating with the underlying blockchain on behalf of client requests. For a client request with explicit authorization, an EP can pre-process requests, inject necessary data, and run the requested smart contract. EP will help to improve UX and increase the quality of services on Klaytn network. The EP provides the following features:

 - Enterprise account paying transaction fees on behalf of clients
 - Request gateway collecting client metrics
 - Security gateway validating and authorizing client requests
 - Data cloaking for a privacy-sensitive transaction.

 By adding EP concept on our platform, we are expecting to offer more practical service and enterprise-friendly platform which can benefit users as well.
