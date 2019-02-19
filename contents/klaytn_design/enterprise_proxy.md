---
title: Enterprise Proxy
sidebar: mydoc_sidebar_docs
permalink: klaytn_design/enterprise_proxy.html
folder: mydoc
---
## Necessity of Enterprise Proxy
Klaytn proposes the Enterprise Proxy (EP) to satisfy the business requirements of enterprise blockchain users and service providers (SPs) while retaining the essential attribute of a blockchain — decentralization. The EP differentiates Klaytn from other platforms because it provides various tools, including Oracle, an application-specific dashboard, and adaptive fee models, and enables SPs to integrate traditional security systems, such as ACLs (access control layers), FWs, and FDSs, which are thought to be difficult to have on a blockchain.

The EP is an off-chain centralized proxy communicating with the underlying blockchain on behalf of client requests. For a client request with explicit authorization, an EP can preprocess requests, inject necessary data, and run the requested smart contracts. Although anyone can become an EP operator, we believe that SPs are most interested in running EPs to promote better UX and to increase the quality of their services. The EP provides the following features:


1. **Enterprise account** paying transaction fees on behalf of clients
2. **Request gateway** collecting client metrics

## Enterprise Account
The enterprise account (EA) is a special purpose account run by an EP allowing an SP to pay gas on behalf of clients. In Klaytn, similar to Ethereum, a transaction should have sufficient gas to run a smart contract. Thus, a client, directly executing smart contracts (i.e., sending a transaction to a smart contract directly), should pay the gas in full from his or her account balance. On the other hand, when a client wishes to run a smart contract maintained by an SP, he or she can receive a subsidy on gas partially or entirely if the transaction is sent to the SP’s EP and if the SP agrees to do so by signing the transaction with a key registered to the EA.

The use of an EA gives flexibility to the SP’s service pricing. For example, a client on a subscription program pays his or her monthly payment for the subscription to the SP and runs the SP’s smart contracts via the EP as many times as the subscription allows. We believe that such flexibility using an EA can help businesses diversify their models.

## Request Gateway
The SP can intentionally limit the entity that can send a trasaction to execute its smart contracts to its EA (e.g., requiring the EA’s signature). In this case, clients must send their transactions to the proxy to run the SP’s contracts, and the SP’s EP can function as a request gateway (RG), where the SP can collect client metrics and place security measures to restrict or authorize client requests.

As mentioned earlier, collecting client metrics in a decentralized environment is nontrivial. However, the RG enables the SP to monitor all client requests that come and go through the EP and collects meaningful metrics, including the number of DApp users, the estimated DApp memory usage, the TPS, the latency, and the average gas price for running contracts. The SP can project the collected metrics on a dashboard to display the app status or trigger other functions or systems to respond to events observed from the metrics. Klaytn will provide a software framework to help SPs implement business intelligence dashboard systems that run the RG to collect metrics and visualize the collected metrics.

