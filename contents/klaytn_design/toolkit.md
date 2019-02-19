---
title: Toolkit
sidebar: mydoc_sidebar_docs
permalink: klaytn_design/toolkit.html
folder: mydoc
---
## Necessity of Toolkit
Well designed infrastructure will be able to mitigate inconveniences to build blockchain based services. Klaytn provides a friendly development environment for developers and enterprises and facilitates a better user experience through toolkits. Klaytn toolkits interface between Klaytn and blockchain based service so that service providers will be able to utilize Klaytn easily. The spectrum of toolkits can be various from a debugger/deployer, transaction profiler to a decentralized exchange.

Starting with providing essential toolkits to build blockchain based services, Klaytn will support more rich toolkits to make service providers to build advanced services efficiently.

## Truffle

[//]: # (TODO: change the name of library)

Since Klaytn supports EVM, smart contract written in Solidity can be compiled and deployed via Truffle.

- [Truffle repository](https://github.com/trufflesuite/truffle)
- [Truffle documents](https://truffleframework.com/docs)

* Truffle install

```
$ npm install -g truffle
```

## caver.js

Klaytn provides JavaScript API which implements the JSON RPC spec of Klaytn. It is also Ethereum compatible API such as web3.js. It is available on npm.

* npm
`package.json` file should exist on the same install path. If it doesn't exist, `package.json` should be generated via `npm init`.

```
$ npm install caver-js
```

* The API specification of caver.js is described at [API/Toolkit API](/api/toolkit.html#caverjs-api-specification).

### Error Code Improvement

The error messages from Ethereum via web3.js are hardly figuring out where the error occurs. caver.js improves the interface to catch error messages from Klaytn.

More details can be found in the value of `txError` of the transaction receipt like the below:

```
Error: runtime error occurred in interpreter
 {
  "blockHash": "0xe7ec35c9fff1178d52cee1d46d40627d19f828c4b06ad1a5c3807698b99acb20",
  "blockNumber": 7811,
  "contractAddress": null,
  "cumulativeGasUsed": 9900000000,
  "from": "0xa8a2d37727197cc0eb827f8c5a3a3aceb26cf59e",
  "gasUsed": 9900000000,
  "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  "status": false,
  "to": "0xf8425b0f65147969621f9390ca06139c7b439497",
  "transactionHash": "0x85ce2b307899c90144442d9b3236827ac57375c522be2435093aebfd920b8c58",
  "transactionIndex": 0,
  "txError": "0x2",
  "events": {}
}
```
- The meaning of error code can be found below:

[//]: #  (https://github.com/ground-x/caver-js/blob/master/packages/caver-core-helpers/src/txErrorTable.js)

```
{
  "0x2": 'runtime error occurred in interpreter',
  "0x3": 'max call depth exceeded',
  "0x4": 'contract address collision',
  "0x5": 'contract creation code storage out of gas',
  "0x6": 'evm: max code size exceeded',
  "0x7": 'out of gas',
  "0x8": 'evm: write protection',
  "0x9": 'evm: execution reverted',
  "0xa": 'reached the opcode count limit',
}
```

[//]: # (TODO: insert 1. error message example, 2. the link of source code)

[//]: ###  JSON RPC Modification

[//]: # The JSON RPCs that the developer wants to use via caver.js are simply modified by setting configuration file.

[//]: # (TODO example)
