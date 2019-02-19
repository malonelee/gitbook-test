---
title: Introduction to the Klaytn Smart Contract
sidebar: mydoc_sidebar_docs
permalink: smart_contract/introduction.html
folder: mydoc
---

[//]: # "NOTE: try to deliver big picture, goal, roadmap"

This document introduces the Klaytn smart contract and describes its definition,
roles, and components.


## The Klaytn Smart Contract

The Klaytn smart contract is a user's program that can implement token
transfers, service logic, games, libraries, or any other code interacting with
the Klaytn blockchain.  When the conditions embedded in the smart contract are
satisfied, the contract is executed immediately.  The terms of the smart
contract are described in the programming language, and the contents of the
established contract are stored as a state.

The state that the smart contract governs is viewed as external memory, and
each execution machine running the smart contract can be abstracted into a
virtual machine (VM) with a library controlling its storage.  This VM can
manipulate storage (*i.e.*, external memory) via a command line interface or
through separately provided libraries.  Given a properly designed abstract
layer, several independent VMs can be accommodated while supporting diverse
environments suited to smart contract development.  In other words, Klaytn's
VMs conceptually separate smart contract executions from external memory
(state) manipulations.

Klaytn provides diverse ways to write smart contracts and to execute them on
the Klaytn network.  As an initial step, Klaytn supports Ethereum's Solidity as
a programming language for Klaytn smart contracts and keeps Ethereum's development
toolkits such as Remix or Truffle interoperable with Klaytn.  Thus, smart
contracts written in Solidity can be compiled with the existing Solidity
compiler and be executed on Klaytn.  The Klaytn team plans to extend Klaytn to
accommodate various execution environments, such as eWASM or WASM, in order to
provide a broader range of potential Klaytn smart contract developers with
their familiar development experience.


## Document Structure

This document is organized as follows:
- [Introduction to the Klaytn Smart Contract](introduction.html)
  gives a brief overview of the Klaytn smart contract.
- [Smart Contract Language](language.html) provides general
  information about the smart contract language, specifically Solidity, which
  is currently the primary programming language for smart contracts on Klaytn.
- [Execution Model and Runtime](execution_model_and_runtime.html) explains
  the underlying mechanism needed for smart contract executions.
- [Klaytn Virtual Machine](klaytn_virtual_machine.html) provides a detailed
  explanation of the Klaytn virtual machine, which executes Klaytn smart
  contracts.


## Conventions

We use the following notations and conventions in this document.
- `A := B`
   - `:=` is used to define `A` as `B`.
- We use the terms "smart contract" and "contract" interchangeably.
