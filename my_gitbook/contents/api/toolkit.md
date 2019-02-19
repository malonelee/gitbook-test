---
title: Toolkit API
sidebar: mydoc_sidebar_docs
permalink: api/toolkit.html
folder: mydoc
---

Since Klaytn keeps compatibility with the development ecosystem of Ethereum, Klaytn supports existing APIs of basic ecosystems. This chapter introduces differences with ecosystems if exists.

## Caution when Sending a Transaction to Klaytn

When broadcasting transactions, beware that the Klaytn Aspen network only
accepts a specific gas price.  Currently the price is fixed to 25 Gpeb
(25 x 10<sup>9</sup> peb).  It returns an error if you attempt to use any other
number for the gas price.  Some examples of setting the gas price to 25 Gpeb
are as follows.

```javascript
// deploy a smart contract via Truffle
module.exports = {
  networks: {
    Klaytn: {
      host: '127.0.0.1', // Klaytn node ip address
      port: 8551, // Klaytn jsonrpc port
      from: '0xabcdabcdabcdabcdabcdabcdabcdabcdabcdabcd', // enter your publickey
      network_id: '1000', // Klaytn network id
      gas: 20000000, // transaction gas limit
      gasPrice: 25000000000, // Aspen network's gasPrice is 25 Gpeb
    },
  },
};
```

```javascript
// call a smart contract function
var KlaytnFaucet = require('./build/contracts/KlaytnFaucet.json');
KlaytnFaucet.methods.getKlay(to).send({
  from: address,
  gas: '9900000000',
  gasPrice: '25000000000',
  chainId: 1000
});
```

```javascript
// create a transaction
const privateKey = '0x0ea86a4535195cac0e03cdc8503ead8002e480dac8b6e18208d03a5666bab3dc';
const address = '0x54718d2EAbeA330f3EA9b9F604f243c404392935';
let txParams = {
  nonce : caver.utils.toHex(0),
  from: address,
  gasPrice: 25000000000,
  gasLimit: 9900000,
  to: '0x7D42EA99f2e72474B70B596130cFC1F4aa080069',
  value: caver.utils.toHex(caver.utils.toPeb("3", "KLAY")),
  chainId: 1000,
};

caver.klay.accounts.signTransaction(txParams, privateKey)
  .then(({ rawTransaction }) => {
    caver.klay.sendSignedTransaction(rawTransaction)
      .on('receipt', console.log)
  });
```

## caver.js API Specification

`caver.js` is a collection of libraries that allow you to interact with a local
or remote Klaytn node, using a HTTP or IPC connection.


## caver.klay

To get consensus information in Klaytn, two APIs have been added:
[caver.klay.getValidators()](#getvalidators) and
[caver.klay.getBlockWithConsensusInfo()](#getblockwithconsensusinfo).


### getValidators

```javascript
caver.klay.getValidators(tag [, callback])
```

Returns a list of all validators at the specified block. If the parameter is not set, returns a list of all validators at the latest block.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| tag | QUANTITY &#124; TAG | (optional) Integer of a block number, or the string `"earliest"` or `"latest"` as in the [default block parameter](/api/platform.html#the-default-block-parameter). |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

| Type | Description |
| --- | --- |
| Array of String | Containing addresses of all validators, each address is 20 bytes. |

**Examples**

```javascript
params: [
   '0x1b4', // 436
]
```

```javascript
> caver.klay.getValidators(() => { console.log });
> caver.klay.getValidators('0x1b4', () => { console.log });
{
   "result":[
      "0x207e38864b45a538733741dc1ff92eff9d1a6159",
      "0x6d64bc82b93368a7f963d6c34483ca3893f405f6",
      "0xbc9c19f91878369776812039e4ebcdfa3c646716",
      "0xe3ed6fa287752b992f936b42360770c59731d9eb"
   ]
}
```

### getBlockWithConsensusInfo

```javascript
caver.klay.getBlockWithConsensusInfo(blockHashOrBlockNumber [, callback])
```

Returns a block with consensus information matched by the given block hash or block number.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| blockHashOrBlockNumber | String &#124; Number | The block hash or block number. Or the string ``"genesis"``, ``"latest"`` or ``"pending"``. |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

``Promise`` returns ``Object`` - A block object with consensus information (a proposer and a list of committee members)
The block object contains:

| Name | Type | Description |
| --- | --- | --- |
| committee | Array | Array of addresses of committee members of this block. The committee is a subset of validators participated in the consensus protocol for this block. |
| gasLimit | QUANTITY | The maximum gas allowed in this block. |
| gasUsed | QUANTITY | The total used gas by all transactions in this block. |
| hash | 32-byte DATA | Hash of the block. `null` when it is a pending block. |
| miner | 20-byte DATA | TBD |
| nonce | 8-byte DATA | TBD |
| number | QUANTITY | The block number. `null` when it is a pending block. |
| parentHash | 32-byte DATA | Hash of the parent block. |
| proposer | 20-byte DATA | The address of the block proposer. |
| receiptsRoot | 32-byte DATA | The root of the receipts trie of the block. |
| size | QUANTITY | Integer the size of this block in bytes. |
| stateRoot | 32-byte DATA | The root of the final state trie of the block. |
| timestamp | QUANTITY | The unix timestamp for when the block was collated. |
| transactions | Array | Array of transaction objects. |
| transactionsRoot | 32-byte DATA | The root of the transaction trie of the block. |

**Examples**

```javascript
params: [
   '0x2f0c986bd224d85411c89c108c28caa6dad1c7f97745051920289c65924faf10',
]
```

```javascript
> caver.klay.getBlockWithConsensusInfo(1, () => { console.log });
{
    "id":73,
    "jsonrpc":"2.0",
    "result": {
        "committee":["0x2352dc74bdf79d29d57c7cebb788dc4e4acfc274","0x2d7ae4e464a179a2da8c23adc5307b57eb0530ba","0x335ab18bc16dbda3b074fe3e07eb47694edb8abb","0x9d9a4565f4be299394b6bd28d267669af18ff9da"],
        "gasLimit":"0xe8d4a50fff",
        "gasUsed":"0x0",
        "hash":"0x2f0c986bd224d85411c89c108c28caa6dad1c7f97745051920289c65924faf10",
        "miner":"0x0000000000000000000000000000000000000000",
        "nonce":"0x0000000000000000",
        "number":"0x1",
        "parentHash":"0x481e2fcfbbe5df1e33511aaee26d9f7460e56df2aa344af965322f905d09c400",
        "proposer":"0x2d7ae4e464a179a2da8c23adc5307b57eb0530ba",
        "receiptsRoot":"0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
        "size":"0x39c",
        "stateRoot":"0x5a4de9ca283535e2d8a07173db9cc42b08265d5e39c87c30523329adae5d9a2a",
        "timestamp":"0x5b9b8eac",
        "transactions":[],
        "transactionsRoot":"0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421"
    }
}
```


### defaultAccount

```javascript
caver.klay.defaultAccount
```

This default address is used as the default `from` property, if no `from`
property is specified in parameters of the following methods:

- [caver.klay.sendTransaction()](#sendtransaction)
- [caver.klay.call()](#call)
- [new caver.klay.Contract()](#new-contract) -> [myContract.methods.myMethod().call()](#methodsmymethodcall)
- [new caver.klay.Contract()](#new-contract) -> [myContract.methods.myMethod().send()](#methodsmymethodsend)

**Property**

20-byte `String` - Any Klaytn address.  You should have the private key for
that address in your node or keystore.  Default is `undefined`.

**Example**

```javascript
> caver.klay.defaultAccount;
undefined

// set the default account
> caver.klay.defaultAccount = '0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe';
```


### defaultBlock

```javascript
caver.klay.defaultBlock
```

The default block is used for certain methods.  You can override it by passing
in the defaultBlock as the last parameter.  The default value is `"latest"`.

- [caver.klay.getBalance()](#getbalance)
- [caver.klay.getCode()](#getcode)
- [caver.klay.getTransactionCount()](#gettransactioncount)
- [caver.klay.getStorageAt()](#getstorageat)
- [caver.klay.call()](#call)
- [new caver.klay.Contract()](#new-contract) -> [myContract.methods.myMethod().call()](#methodsmymethodcall)

**Property**

Default block parameters can be one of the following:

- Number: A block number
- `"genesis"` - String: The genesis block
- `"latest"` - String: The latest block (current head of the blockchain)
- `"pending"` - String: The currently mined block (including pending transactions)

Default is `"latest"`.

**Example**

```javascript
> caver.klay.defaultBlock;
"latest"

// set the default block
> caver.klay.defaultBlock = 1000;
```


### isSyncing

```javascript
caver.klay.isSyncing([callback])
```

Checks if the node is currently syncing and returns either a syncing object or `false`.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |


**Return Value**

`Promise` returns `Object|Boolean` - A sync object when the node is currently syncing or `false`:

| Name | Type | Description |
| --- | --- | --- |
| startingBlock | Number | The block number where the sync started. |
| currentBlock | Number | The block number where at which block the node currently synced to already. |
| highestBlock | Number | The estimated block number to sync to. |
| knownStates | Number | The estimated states to download. |
| pulledStates | Number | The already downloaded states. |

**Example**

```javascript
> caver.klay.isSyncing().then(console.log);
{
    startingBlock: 100,
    currentBlock: 312,
    highestBlock: 512,
    knownStates: 234566,
    pulledStates: 123455
}
```


### getAccounts

```javascript
caver.klay.getAccounts([callback])
```

Returns a list of accounts that the node controls.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

`Promise` returns `Array` - An array of addresses controlled by node.

**Example**

```javascript
> caver.klay.getAccounts().then(console.log);
["0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe", "0xDCc6960376d6C6dEa93647383FfB245CfCed97Cf"]
```


### getBlockNumber

```javascript
caver.klay.getBlockNumber([callback])
```

Returns the current block number.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

`Promise` returns `Number` - The number of the most recent block.

**Example**

```javascript
> caver.klay.getBlockNumber().then(console.log);
2744
```


### getBalance

```javascript
caver.klay.getBalance(address [, defaultBlock] [, callback])
```
Gets the balance of an address at a given block.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| address | String | The address to get the balance of. |
| defaultBlock | Number &#124; String | (optional) If you pass this parameter it will not use the default block set with [caver.klay.defaultBlock](#defaultblock). |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

``Promise`` returns ``String`` - The current balance for the given address in peb.

**Example**

```javascript
> caver.klay.getBalance("0x407d73d8a49eeb85d32cf465507dd71d507100c1").then(console.log);
"1000000000000"
```

### getStorageAt

```javascript
caver.klay.getStorageAt(address, position [, defaultBlock] [, callback])
```
Gets the storage at a specific position of an address.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| address | String | The address to get the storage from. |
| position | Number | The index position of the storage. |
| defaultBlock | Number &#124; String | (optional) If you pass this parameter it will not use the default block set with [caver.klay.defaultBlock](#defaultblock). |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

``Promise`` returns ``String`` - The value in storage at the given position.

**Example**

```javascript
> caver.klay.getStorageAt("0x407d73d8a49eeb85d32cf465507dd71d507100c1", 0).then(console.log);
"0x033456732123ffff2342342dd12342434324234234fd234fd23fd4f23d4234"
```

### getCode

```javascript
caver.klay.getCode(address [, defaultBlock] [, callback])
```
Gets the code at a specific address.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| address | String | The address to get the code from. |
| defaultBlock | Number &#124; String | (optional) If you pass this parameter it will not use the default block set with [caver.klay.defaultBlock](#defaultblock). |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

``Promise`` returns ``String`` - The data at given address ``address``.

**Example**

```javascript
> caver.klay.getCode("0xd5677cf67b5aa051bb40496e68ad359eb97cfbf8").then(console.log);
"0x600160008035811a818181146012578301005b601b6001356025565b8060005260206000f25b600060078202905091905056"
```

### getBlock

```javascript
caver.klay.getBlock(blockHashOrBlockNumber [, returnTransactionObjects] [, callback])
```
Returns a block matching the block hash or block number.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| blockHashOrBlockNumber | String &#124; Number | The block hash or block number. Or the string ``"genesis"``, ``"latest"`` or ``"pending"``. |
| returnTransactionObjects | Boolean | (optional, default ``false``) If ``true``, the returned block will contain all transactions as objects, if ``false`` it will only contains the transaction hashes. |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

``Promise`` returns ``Object`` - The block object:

| Name | Type | Description |
| --- | --- | --- |
| number | Number | The block number. ``null`` when it is a pending block. |
| hash | 32-byte String | Hash of the block. ``null`` when it is a pending block. |
| parentHash | 32-byte String | Hash of the parent block. |
| nonce | 8-byte String | Hash of the generated proof-of-work. ``null`` when it is a pending block. |
| logsBloom | 256-byte String | The bloom filter for the logs of the block. ``null`` when it is a pending block. |
| transactionsRoot | 32-byte String | The root of the transaction trie of the block. |
| stateRoot | 32-byte String | The root of the final state trie of the block. |
| miner | String | The address of the beneficiary to whom the mining rewards were given. |
| difficulty | String | Integer of the difficulty for this block. |
| totalDifficulty | String | Integer of the total difficulty of the chain until this block. |
| extraData | String | The "extra data" field of this block. |
| size | Number | Integer the size of this block in bytes. |
| gasLimit | Number | The maximum gas allowed in this block. |
| gasUsed | Number | The total used gas by all transactions in this block. |
| timestamp | Number | The unix timestamp for when the block was collated. |
| transactions | Array | Array of transaction objects, or 32-byte transaction hashes depending on the ``returnTransactionObjects`` parameter. |
| receiptsRoot | 32-byte DATA | The root of the receipts trie of the block. |

**Example**

```javascript
> caver.klay.getBlock(3150).then(console.log);
{
    "number": 3,
    "hash": "0xef95f2f1ed3ca60b048b4bf67cde2195961e0bba6f70bcbea9a2c4e133e34b46",
    "parentHash": "0x2302e1c0b972d00932deb5dab9eb2982f570597d9d42504c05d9c2147eaf9c88",
    "nonce": "0xfb6e1a62d119228b",
    "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
    "transactionsRoot": "0x3a1b03875115b79539e5bd33fb00d8f7b7cd61929d5a3c574f507b8acf415bee",
    "stateRoot": "0xf1133199d44695dfa8fd1bcfe424d82854b5cebef75bddd7e40ea94cda515bcb",
    "miner": "0x8888f1f195afa192cfee860698584c030f4c9db1",
    "difficulty": '21345678965432',
    "totalDifficulty": '324567845321',
    "size": 616,
    "extraData": "0x",
    "gasLimit": 3141592,
    "gasUsed": 21662,
    "timestamp": 1429287689,
    "transactions": [
        "0x9fc76417374aa880d4449a1f7f31ec597f00b1f6f3dd2d66f4c9c6c445836d8b"
    ],
}
```

### getBlockTransactionCount

```javascript
caver.klay.getBlockTransactionCount(blockHashOrBlockNumber [, callback])
```
Returns the number of transaction in a given block.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| blockHashOrBlockNumber | String &#124; Number | The block number or hash. Or the string ``"genesis"``, ``"latest"`` or ``"pending"``. |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

``Promise`` returns ``Number`` - The number of transactions in the given block.

**Example**

```javascript
> caver.klay.getBlockTransactionCount("0x407d73d8a49eeb85d32cf465507dd71d507100c1").then(console.log);
1
```

### getTransaction

```javascript
caver.klay.getTransaction(transactionHash [, callback])
```
Returns a transaction matching the given transaction hash.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| transactionHash | String | The transaction hash. |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

`Promise` returns `Object` - A transaction object its hash `transactionHash`:

| Name | Type | Description |
| --- | --- | --- |
| hash | 32-byte String | Hash of the transaction. |
| nonce | Number | The number of transactions made by the sender prior to this one. |
| blockHash | 32-byte String | Hash of the block where this transaction was in. ``null`` when its pending. |
| blockNumber | Number | Block number where this transaction was in. ``null`` when its pending. |
| transactionIndex | Number | Integer of the transactions index position in the block. ``null`` when its pending. |
| from | String | Address of the sender. |
| to | String | Address of the receiver. ``null`` when its a contract creation transaction. |
| value | String | Value transferred in :ref:`peb <what-is-peb>`. |
| gasPrice | String | Gas price provided by the sender in :ref:`peb <what-is-peb>`. |
| gas | Number | Gas provided by the sender. |
| input | String | The data sent along with the transaction. |


**Example**

```javascript
> caver.klay.getTransaction('0x9fc76417374aa880d4449a1f7f31ec597f00b1f6f3dd2d66f4c9c6c445836d8b§234')
  .then(console.log);
{
    "hash": "0x9fc76417374aa880d4449a1f7f31ec597f00b1f6f3dd2d66f4c9c6c445836d8b",
    "nonce": 2,
    "blockHash": "0xef95f2f1ed3ca60b048b4bf67cde2195961e0bba6f70bcbea9a2c4e133e34b46",
    "blockNumber": 3,
    "transactionIndex": 0,
    "from": "0xa94f5374fce5edbc8e2a8697c15331677e6ebf0b",
    "to": "0x6295ee1b4f6dd65047762f924ecd367c17eabf8f",
    "value": '123450000000000000',
    "gas": 314159,
    "gasPrice": '2000000000000',
    "input": "0x57cb2fc4"
}
```

### getTransactionFromBlock

```javascript
caver.klay.getTransactionFromBlock(hashStringOrNumber, indexNumber [, callback])
```

Returns a transaction based on a block hash or number and the transactions index position.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| hashStringOrNumber | String | A block number or hash. Or the string ``"genesis"``, ``"latest"`` or ``"pending"``. |
| indexNumber | Number | The transactions index position. |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

`Object` - A transaction object, see [caver.klay.getTransaction](#gettransaction)

**Examples**

```javascript
> caver.klay.getTransactionFromBlock('0x4534534534', 2).then(console.log);
// see caver.klay.getTransaction
```

### getTransactionReceipt

```javascript
caver.klay.getTransactionReceipt(hash [, callback])
```
Returns the receipt of a transaction by transaction hash.


**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| hash | String | The transaction hash |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

`Object` - A transaction receipt object, or `null` when no receipt was found:

| Name | Type | Description |
| --- | --- | --- |
| status | Boolean | `true` if the transaction was successful, `false` if the Klaytn Virtual Machine reverted the transaction. |
| blockHash | 32-byte String | Hash of the block where this transaction was in. |
| blockNumber | Number | Block number where this transaction was in. |
| transactionHash | 32-byte String | Hash of the transaction. |
| transactionIndex | Number | Integer of the transactions index position in the block. |
| from | String | Address of the sender. |
| to | String | Address of the receiver. ``null`` when its a contract creation transaction. |
| contractAddress | String | The contract address created, if the transaction was a contract creation, otherwise ``null``. |
| cumulativeGasUsed | Number | The total amount of gas used when this transaction was executed in the block. |
| gasUsed | Number | The amount of gas used by this specific transaction alone. |
| logs | Array | Array of log objects, which this transaction generated. |
| logsBloom | 256-byte String | The bloom filter for the logs of the block. ``null`` when it is a pending block. |

**Example**

```javascript
> caver.klay.getTransactionReceipt('0x9fc76417374aa880d4449a1f7f31ec597f00b1f6f3dd2d66f4c9c6c445836d8b')
  .then(console.log);
{
  "status": true,
  "transactionHash": "0x9fc76417374aa880d4449a1f7f31ec597f00b1f6f3dd2d66f4c9c6c445836d8b",
  "transactionIndex": 0,
  "blockHash": "0xef95f2f1ed3ca60b048b4bf67cde2195961e0bba6f70bcbea9a2c4e133e34b46",
  "blockNumber": 3,
  "contractAddress": "0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe",
  "cumulativeGasUsed": 314159,
  "gasUsed": 30234,
  "logs": [{
     // logs as returned by getPastLogs, etc.
  }, ...]
}
```


### getTransactionCount

```javascript
caver.klay.getTransactionCount(address [, defaultBlock] [, callback])
```
Gets the number of transactions sent from this address.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| address | String | The address to get the number of transactions from. |
| defaultBlock | Number &#124; String | (optional) If you pass this parameter it will not use the default block set with [caver.klay.defaultBlock](#defaultblock). |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

| Type | Description |
| --- | --- |
| Number | The number of transactions sent from the given address. |

**Example**

```javascript
> caver.klay.getTransactionCount("0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe")
  .then(console.log);
1
```


### sendTransaction

```javascript
caver.klay.sendTransaction(transactionObject [, callback])
```
Sends a transaction to the network.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| transactionObject | Object | The transaction object to send. |
| transactionObject.to | String | (optional) The destination address of the message, left undefined for a contract-creation transaction. |
| transactionObject.value | Number &#124; String &#124; BN &#124; BigNumber | (optional) The value transferred for the transaction in peb, also the endowment if it's a contract-creation transaction. |
| transactionObject.gas | Number | (optional, default: TBD) The amount of gas to use for the transaction (unused gas is refunded). |
| transactionObject.data | String | (optional) Either an [ABI byte string](http://solidity.readthedocs.io/en/latest/abi-spec.html) containing the data of the function call on a contract, or in the case of a contract-creation transaction the initialization code. |
| transactionObject.nonce | Number | (optional) Integer of a nonce. This allows to overwrite your own pending transactions that use the same nonce. |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

The `callback` will return the 32-byte transaction hash.

`PromiEvent`: A promise combined event emitter. Will be resolved when the transaction receipt is available. Additionally the following events are available:

- ``"transactionHash"`` returns ``String``: Is fired right after the transaction is sent and a transaction hash is available.
- ``"receipt"`` returns ``Object``: Is fired when the transaction receipt is available.
- ``"error"`` returns ``Error``: Is fired if an error occurs during sending. On an out-of-gas error, the second parameter is the receipt.

**Example**

```javascript
var code = "603d80600c6000396000f3007c01000000000000000000000000000000000000000000000000000000006000350463c6888fa18114602d57005b6007600435028060005260206000f3";

// using the callback
caver.klay.sendTransaction({
    from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe',
    data: code // deploying a contracrt
}, function(error, hash){
    ...
});

// using the promise
caver.klay.sendTransaction({
    from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe',
    to: '0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe',
    value: '1000000000000000'
})
.then(function(receipt){
    ...
});

// using the event emitter
caver.klay.sendTransaction({
    from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe',
    to: '0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe',
    value: '1000000000000000'
})
.on('transactionHash', function(hash){
    ...
})
.on('receipt', function(receipt){
    ...
})
.on('error', console.error); // If an out-of-gas error, the second parameter is the receipt.
```


### sendSignedTransaction
Sends an already signed transaction, generated using `caver.klay.accounts.signTransaction`
```javascript
caver.klay.sendSignedTransaction(signedTransactionData [, callback])
```

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| signedTransactionData | String | Signed transaction data in HEX format. |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

| Type | Description |
| --- | --- |
| PromiEvent | A promise combined event emitter. Will be resolved when the transaction receipt is available. |

For PromiEvent, the following events are available:

- ``"transactionHash"`` returns ``String``: Is fired right after the transaction is sent and a transaction hash is available.
- ``"receipt"`` returns ``Object``: Is fired when the transaction receipt is available.
- ``"error"`` returns ``Error``: Is fired if an error occurs during sending. On an out-of-gas error, the second parameter is the receipt.

**Example**

```javascript
const privateKey = '0x24649b229c4aa832b66d8ce419f29336785586374fd21975ebcf1126198b1016';

const txObject = {
  gasPrice: '25000000000',
  gasLimit: '30000',
  to: '0x0000000000000000000000000000000000000000',
  data: '0xff',
  value: caver.utils.toPeb(1, 'KLAY'),
};

caver.klay.accounts.signTransaction(txObject, privateKey)
  .then(({ rawTransaction }) => {
    caver.klay.sendSignedTransaction(rawTransaction)
      .on('receipt', console.log)
  });
```


### signTransaction

```javascript
caver.klay.signTransaction(transactionObject [, callback])
```
Signs a transaction. This account needs to be unlocked.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| transactionObject | Object | The transaction data to sign. |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

`Promise` returns `Object` - The RLP encoded transaction. The `raw` property can be used to send the transaction using [caver.klay.sendSignedTransaction](#sendsignedtransaction).

**Example**

```javascript
> caver.klay.signTransaction({
    nonce: 0,
    from: "0xEB014f8c8B418Db6b45774c326A0E64C78914dC0",
    gasPrice: '25000000000',
    gas: "21000",
    to: '0x3535353535353535353535353535353535353535',
    value: "1000000000000000000",
    data: ""
}).then(console.log);

{
    raw: '0xf86c808504a817c800825208943535353535353535353535353535353535353535880de0b6b3a76400008025a04f4c17305743700648bc4f6cd3038ec6f6af0df73e31757007b7f59df7bee88da07e1941b264348e80c78c4027afc65a87b0a5e43e86742b8ca0823584c6788fd0',
    tx: {
        nonce: '0x0',
        gasPrice: '25000000000',
        gas: '0x5208',
        to: '0x3535353535353535353535353535353535353535',
        value: '0xde0b6b3a7640000',
        input: '0x',
        v: '0x25',
        r: '0x4f4c17305743700648bc4f6cd3038ec6f6af0df73e31757007b7f59df7bee88d',
        s: '0x7e1941b264348e80c78c4027afc65a87b0a5e43e86742b8ca0823584c6788fd0',
        hash: '0xda3be87732110de6c1354c83770aae630ede9ac308d9f7b399ecfba23d923384'
    }
}
```


### call

```javascript
caver.klay.call(callObject [, defaultBlock] [, callback])
```
Executes a message call transaction, which is directly executed in the Klaytn
Virtual Machine of the node, but never mined into the blockchain.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| callObject | Object | A transaction object with the difference that for calls the from property is optional as well. |
| defaultBlock | Number &#124; String | (optional) If you pass this parameter it will not use the default block set with [caver.klay.defaultBlock](#defaultblock). |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

``Promise`` returns ``String``: The returned data of the call, *e.g.*, a smart contract functions return value.

**Example**

```javascript
> caver.klay.call({
    to: "0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe", // contract address
    data: "0xc6888fa10000000000000000000000000000000000000000000000000000000000000003"
})
.then(console.log);

"0x000000000000000000000000000000000000000000000000000000000000000a"
```


### getGasPrice

```javascript
caver.klay.getGasPrice([callback])
```

Returns the unit price defined in the Klaytn network.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

`Promise` returns `String` - Number string of the current unit price in peb.

**Example**

```javascript
> caver.klay.getGasPrice().then(console.log);
"25000000000"
```


### estimateGas

```javascript
caver.klay.estimateGas(callObject [, callback])
```
Executes a message call or transaction and returns the amount of the gas used for the simulated call/transaction.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| callObject | Object | A transaction object with the difference that for calls the from property is optional as well. |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

``Promise`` returns ``Number`` - the used gas for the simulated call/transaction.

**Example**

```javascript
> caver.klay.estimateGas({
    to: "0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe",
    data: "0xc6888fa10000000000000000000000000000000000000000000000000000000000000003"
})
.then(console.log);

40
```


### getPastLogs

```javascript
caver.klay.getPastLogs(options [, callback])
```
Gets past logs, matching the given options.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| options | Object | The filter options. |
| options.fromBlock | Number &#124; String | The number of the earliest block (``"latest"`` may be given to mean the most recent and ``"pending"`` currently mining, block). By default ``"latest"``. |
| options.toBlock | Number &#124; String | The number of the latest block (``"latest"`` may be given to mean the most recent and ``"pending"`` currently mining, block). By default ``"latest"``. |
| options.address | String &#124; Array | An address or a list of addresses to only get logs from particular account(s). |
| options.topics | Array | An array of values that must each appear in the log entries. The order is important, if you want to leave topics out use ``null``, *e.g.*, ``[null, '0x12...']``. You can also pass an array for each topic with options for that topic *e.g.,* ``[null, ['option1', 'option2']]``. |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

``Promise`` returns ``Array`` - Array of log objects.

The structure of the returned event ``Object`` in the ``Array`` looks as follows:

| Name | Type | Description |
| --- | --- | --- |
| address | String | From which this event originated from. |
| data | String | The data containing non-indexed log parameter. |
| topics | Array | An array with max 4 32-byte topics, topic 1-3 contains indexed parameters of the log. |
| logIndex | Number | Integer of the event index position in the block. |
| transactionIndex | Number | Integer of the transaction's index position, the event was created in. |
| transactionHash | 32-byte String | Hash of the transaction this event was created in. |
| blockHash | 32-byte String | Hash of the block where this event was created in. ``null`` when its still pending. |
| blockNumber | Number | The block number where this log was created in. ``null`` when still pending. |
| id | String | A log identifier. It is made through concatenating "log_" string with `keccak256(blockHash + transactionHash + logIndex).substr(0, 8)` |

**Example**

```javascript
> caver.klay.getPastLogs({
    address: "0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe",
    topics: ["0x033456732123ffff2342342dd12342434324234234fd234fd23fd4f23d4234"]
})
.then(console.log);

[{
    data: '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385',
    topics: ['0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7', '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385']
    logIndex: 0,
    transactionIndex: 0,
    transactionHash: '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385',
    blockHash: '0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7',
    blockNumber: 1234,
    address: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe',
    id: 'log_124d61bc',
},{...}]
```

## caver.utils

`caver.utils` provides utility functions.


### randomHex

```javascript
caver.utils.randomHex(size)
```
The [randomHex](https://github.com/frozeman/randomHex) library to generate cryptographically strong pseudo-random HEX strings from a given byte size.

**Parameters**

| Name | Type | Description |
| ---- | ---- | ----------- |
| size | Number | The byte size for the HEX string, *e.g.*, ``32`` will result in a 32-byte HEX string with 64 characters preficed with "0x". |

**Return Value**

| Type | Description |
| ---- | ----------- |
| String | The generated random HEX string. |

**Example**

```javascript
> caver.utils.randomHex(32);
"0xa5b9d60f32436310afebcfda832817a68921beb782fabf7915cc0460b443116a"

> caver.utils.randomHex(4);
"0x6892ffc6"

> caver.utils.randomHex(2);
"0x99d6"

> caver.utils.randomHex(1);
"0x9a"

> caver.utils.randomHex(0);
"0x"
```

### _

```javascript
caver.utils._()
```

The [underscore](http://underscorejs.org) library for many convenience JavaScript functions.

See the [underscore API reference](http://underscorejs.org) for details.

**Example**

```javascript
> var _ = caver.utils._;

> _.union([1,2],[3]);
[1,2,3]

> _.each({my: 'object'}, function(value, key){ ... });
...
```


### BN

```javascript
caver.utils.BN(mixed)
```
The [BN.js](https://github.com/indutny/bn.js/) library for calculating with big numbers in JavaScript.
See the [BN.js documentation](https://github.com/indutny/bn.js/) for details.

**Parameters**

| Name | Type | Description |
| ---- | ---- | ----------- |
| mixed | String &#124; Number| A number, number string or HEX string to convert to a BN object. |

**Return Value**

| Type | Description |
| ---- | ----------- |
| Object | The [BN.js](https://github.com/indutny/bn.js/) instance. |

**Example**

```javascript
> var BN = caver.utils.BN;

> new BN(1234).toString();
"1234"

> new BN('1234').add(new BN('1')).toString();
"1235"

> new BN('0xea').toString();
"234"
```


### isBN

```javascript
caver.utils.isBN(bn)
```

Checks if a given value is a [BN.js](https://github.com/indutny/bn.js/) instance.


**Parameters**

| Name | Type | Description |
| ---- | ---- | ----------- |
| bn   | Object | A [BN.js](https://github.com/indutny/bn.js/) instance. |

**Return Value**

| Type | Description |
| ---- | ----------- |
| Boolean | `true` if a given value is a [BN.js](https://github.com/indutny/bn.js/) instance. |

**Example**

```javascript
> var number = new BN(10);
> caver.utils.isBN(number);
true
```


### isBigNumber

```javascript
caver.utils.isBigNumber(bignumber)
```

Checks if a given value is a [BigNumber.js](http://mikemcl.github.io/bignumber.js/) instance.


**Parameters**

| Name | Type | Description |
| ---- | ---- | ----------- |
| bignumber | Object | A [BigNumber.js](http://mikemcl.github.io/bignumber.js/) instance. |

**Return Value**

| Type | Description |
| ---- | ----------- |
| Boolean | `true` if a given value is a `BigNumber.js` instance. |

**Example**

```javascript
> var number = new BigNumber(10);
> caver.utils.isBigNumber(number);
true
```


### sha3

```javascript
caver.utils.sha3(string)
caver.utils.keccak256(string) // ALIAS
```
Calculates the sha3 of the input.

**NOTE**: To mimic the sha3 behavior of Solidity use [caver.utils.soliditySha3](#soliditysha3).

**Parameters**

| Name | Type | Description |
| ---- | ---- | ----------- |
| string | String | A string to hash. |

**Return Value**

| Type | Description |
| ---- | ----------- |
| String | The result hash. |

**Example**

```javascript
> caver.utils.sha3('234'); // taken as string
"0xc1912fee45d61c87cc5ea59dae311904cd86b84fee17cc96966216f811ce6a79"

> caver.utils.sha3(new BN('234')); // utils.sha3 stringify bignumber instance.
"0xc1912fee45d61c87cc5ea59dae311904cd86b84fee17cc96966216f811ce6a79"

> caver.utils.sha3(234);
null // can't calculate the has of a number

> caver.utils.sha3(0xea); // same as above, just the HEX representation of the number
null

> caver.utils.sha3('0xea'); // will be converted to a byte array first, and then hashed
"0x2f20677459120677484f7104c76deb6846a2c071f9b3152c103bb12cd54d1a4a"
```

### soliditySha3


```javascript
caver.utils.soliditySha3(param1 [, param2, ...])
```

Calculates the sha3 of given input parameters in the same way solidity would.
This means arguments will be ABI converted and tightly packed before being hashed.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| paramX | Mixed| Any type, or an object with ``{type: 'uint', value: '123456'}`` or ``{t: 'bytes', v: '0xfff456'}``. Basic types are autodetected as follows:<br> - ``String`` non numerical UTF-8 string is interpreted as ``string``.<br> - ``String|Number|BN|HEX`` positive number is interpreted as ``uint256``.<br> - ``String|Number|BN`` negative number is interpreted as ``int256``.<br> - ``Boolean`` as ``bool``.<br> - ``String`` HEX string with leading ``0x`` is interpreted as ``bytes``.<br> - ``HEX`` HEX number representation is interpreted as ``uint256``.<br>|

**Return Value**

| Type | Description |
| ---- | ----------- |
| String | The result hash. |

**Example**

```javascript
> caver.utils.soliditySha3('234564535', '0xfff23243', true, -10);
// auto detects: uint256, bytes, bool, int256
"0x3e27a893dc40ef8a7f0841d96639de2f58a132be5ae466d40087a2cfa83b7179"

> caver.utils.soliditySha3('Hello!%'); // auto detects: string
"0x661136a4267dba9ccdf6bfddb7c00e714de936674c4bdb065a531cf1cb15c7fc"

> caver.utils.soliditySha3('234'); // auto detects: uint256
"0x61c831beab28d67d1bb40b5ae1a11e2757fa842f031a2d0bc94a7867bc5d26c2"

> caver.utils.soliditySha3(0xea); // same as above
"0x61c831beab28d67d1bb40b5ae1a11e2757fa842f031a2d0bc94a7867bc5d26c2"

> caver.utils.soliditySha3(new BN('234')); // same as above
"0x61c831beab28d67d1bb40b5ae1a11e2757fa842f031a2d0bc94a7867bc5d26c2"

> caver.utils.soliditySha3({type: 'uint256', value: '234'})); // same as above
"0x61c831beab28d67d1bb40b5ae1a11e2757fa842f031a2d0bc94a7867bc5d26c2"

> caver.utils.soliditySha3({t: 'uint', v: new BN('234')})); // same as above
"0x61c831beab28d67d1bb40b5ae1a11e2757fa842f031a2d0bc94a7867bc5d26c2"

> caver.utils.soliditySha3('0x407D73d8a49eeb85D32Cf465507dd71d507100c1');
"0x4e8ebbefa452077428f93c9520d3edd60594ff452a29ac7d2ccc11d47f3ab95b"

> caver.utils.soliditySha3({t: 'bytes', v: '0x407D73d8a49eeb85D32Cf465507dd71d507100c1'});
"0x4e8ebbefa452077428f93c9520d3edd60594ff452a29ac7d2ccc11d47f3ab95b" // same result as above

> caver.utils.soliditySha3({t: 'address', v: '0x407D73d8a49eeb85D32Cf465507dd71d507100c1'});
"0x4e8ebbefa452077428f93c9520d3edd60594ff452a29ac7d2ccc11d47f3ab95b" // same as above, but will do a checksum check, if its multi case

> caver.utils.soliditySha3({t: 'bytes32', v: '0x407D73d8a49eeb85D32Cf465507dd71d507100c1'});
"0x3c69a194aaf415ba5d6afca734660d0a3d45acdc05d54cd1ca89a8988e7625b4" // different result as above

> caver.utils.soliditySha3({t: 'string', v: 'Hello!%'}, {t: 'int8', v:-23}, {t: 'address', v: '0x85F43D8a49eeB85d32Cf465507DD71d507100C1d'});
"0xa13b31627c1ed7aaded5aecec71baf02fe123797fffd45e662eac8e06fbe4955"
```

### isHex

```javascript
caver.utils.isHex(hex)
```
Checks if a given string is a HEX string.

**Parameters**

| Name | Type | Description |
| ---- | ---- | ----------- |
|hex | String &#124; HEX | The given HEX string. |

**Return Value**

| Type | Description |
| ---- | ----------- |
| Boolean | `true` if a given string is a HEX string. |

**Example**

```javascript
> caver.utils.isHex('0xc1912');
true

> caver.utils.isHex(0xc1912);
true

> caver.utils.isHex('c1912');
true

> caver.utils.isHex(345);
true // this is tricky, as 345 can be a HEX representation or a number, be careful when not having a 0x in front!

> caver.utils.isHex('0xZ1912');
false

> caver.utils.isHex('Hello');
false
```

### isHexStrict


```javascript
caver.utils.isHexStrict(hex)
```
Checks if a given string is a HEX string. Difference to [caver.utils.isHex](#ishex) is that it expects HEX to be prefixed with ``0x``.

**Parameters**

| Name | Type | Description |
| ---- | ---- | ----------- |
| hex | String &#124; HEX | The given HEX string. |

**Return Value**

| Type | Description |
| ---- | ----------- |
| Boolean | `true` if a given string is a HEX string. |


**Example**

```javascript
> caver.utils.isHexStrict('0xc1912');
true

> caver.utils.isHexStrict(0xc1912);
false

> caver.utils.isHexStrict('c1912');
false

> caver.utils.isHexStrict(345);
false // this is tricky, as 345 can be a HEX representation or a number, be careful when not having a 0x in front!

> caver.utils.isHexStrict('0xZ1912');
false

> caver.utils.isHex('Hello');
false
```

### isAddress

```javascript
caver.utils.isAddress(address)
```
Checks if a given string is a valid Klaytn address.
It will also check the checksum, if the address has upper and lowercase letters.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| address | String | An address string. |

**Return Value**

| Type | Description |
| --- | --- |
| Boolean | `true` if a given string is a valid Klaytn address. |

**Examples**

```javascript
> caver.utils.isAddress('0xc1912fee45d61c87cc5ea59dae31190fffff232d');
true

> caver.utils.isAddress('c1912fee45d61c87cc5ea59dae31190fffff232d');
true

> caver.utils.isAddress('0XC1912FEE45D61C87CC5EA59DAE31190FFFFF232D');
true // as all is uppercase, no checksum will be checked

> caver.utils.isAddress('0xc1912fEE45d61C87Cc5EA59DaE31190FFFFf232d');
true

> caver.utils.isAddress('0xC1912fEE45d61C87Cc5EA59DaE31190FFFFf232d');
false // wrong checksum
```


### toChecksumAddress

```javascript
caver.utils.toChecksumAddress(address)
```
Converts an upper or lowercase Klaytn address to a checksum address.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| address | String | An address string. |

**Return Value**

| Type | Description |
| --- | --- |
| String | The checksum address. |

**Examples**

```javascript
> caver.utils.toChecksumAddress('0xc1912fee45d61c87cc5ea59dae31190fffff232d');
"0xc1912fEE45d61C87Cc5EA59DaE31190FFFFf232d"

> caver.utils.toChecksumAddress('0XC1912FEE45D61C87CC5EA59DAE31190FFFFF232D');
"0xc1912fEE45d61C87Cc5EA59DaE31190FFFFf232d" // same as above
```

### checkAddressChecksum

```javascript
caver.utils.checkAddressChecksum(address)
```
Checks the checksum of a given address. Will also return `false` on non-checksum addresses.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| address | String | An address string.|

**Return Value**

| Type | Description |
| --- | --- |
| Boolean | ``true`` when the checksum of the address is valid, ``false`` if it is not a checksum address, or the checksum is invalid. |

**Examples**

```javascript
> caver.utils.checkAddressChecksum('0xc1912fEE45d61C87Cc5EA59DaE31190FFFFf232d');
true
```


### toHex

```javascript
caver.utils.toHex(mixed)
```
Converts any given value to HEX.
Number strings will interpreted as numbers.
Text strings will be interpreted as UTF-8 strings.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| mixed | String &#124; Number &#124; BN &#124; BigNumber | The input to convert to HEX. |

**Return Value**

| Type | Description |
| --- | --- |
| String | The resulting HEX string. |

**Examples**

```javascript
> caver.utils.toHex('234');
"0xea"

> caver.utils.toHex(234);
"0xea"

> caver.utils.toHex(new BN('234'));
"0xea"

> caver.utils.toHex(new BigNumber('234'));
"0xea"

> caver.utils.toHex('I have 100€');
"0x49206861766520313030e282ac"
```


### toBN

```javascript
caver.utils.toBN(number)
```
Safely converts any given value (including [BigNumber.js](http://mikemcl.github.io/bignumber.js/) instances) into a [BN.js](https://github.com/indutny/bn.js/) instance, for handling big numbers in JavaScript.

**NOTE**: For just the [BN.js](https://github.com/indutny/bn.js/) class, use [caver.utils.BN](#bn).


**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| number | String &#124; Number &#124; HEX| Number to convert to a big number. |

**Return Value**

| Type | Description |
| --- | --- |
| Object| The [BN.js](https://github.com/indutny/bn.js/) instance. |

**Examples**

```javascript
> caver.utils.toBN(1234).toString();
"1234"

> caver.utils.toBN('1234').add(caver.utils.toBN('1')).toString();
"1235"

> caver.utils.toBN('0xea').toString();
"234"
```


### hexToNumberString

```javascript
caver.utils.hexToNumberString(hex)
```
Returns the number representation of a given HEX value as a string.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| hexString | HEX String | A HEX string to be converted. |

**Return Value**

| Type | Description |
| --- | --- |
| String | The number as a string. |

**Examples**

```javascript
> caver.utils.hexToNumberString('0xea');
"234"
```

### hexToNumber

```javascript
caver.utils.hexToNumber(hex)
```
Returns the number representation of a given HEX value.

**NOTE**: This is not useful for big numbers, rather use [caver.utils.toBN](#tobn).

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| hexString | HEX String | A HEX string to be converted. |

**Return Value**

| Type | Description |
| --- | --- |
| Number | The number representation of a given HEX value. |

**Examples**

```javascript
> caver.utils.hexToNumber('0xea');
234
```


### numberToHex

```javascript
caver.utils.numberToHex(number)
```
Returns the HEX representation of a given number value.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| number | String &#124; Number &#124; BN &#124; BigNumber | A number as string or number. |

**Return Value**

| Type | Description |
| --- | --- |
| String | The HEX value of the given number. |

**Examples**

```javascript
> caver.utils.numberToHex('234');
'0xea'
```


### hexToUtf8

```javascript
caver.utils.hexToUtf8(hex)
caver.utils.hexToString(hex) // ALIAS
```
Returns the UTF-8 string representation of a given HEX value.


**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| hex | String| A HEX string to convert to a UTF-8 string. |

**Return Value**

| Type | Description |
| --- | --- |
| String | The UTF-8 string. |

**Examples**

```javascript
> caver.utils.hexToUtf8('0x49206861766520313030e282ac');
"I have 100€"
```


### hexToAscii

```javascript
caver.utils.hexToAscii(hex)
```
Returns the ASCII string representation of a given HEX value.


**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| hex | String | A HEX string to convert to a ASCII string. |

**Return Value**

| Type | Description |
| --- | --- |
| String | The ASCII string. |

**Examples**

```javascript
> caver.utils.hexToAscii('0x4920686176652031303021');
"I have 100!"
```


### utf8ToHex

```javascript
caver.utils.utf8ToHex(string)
caver.utils.stringToHex(string) // ALIAS
```
Returns the HEX representation of a given UTF-8 string.


**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| string | String | A UTF-8 string to convert to a HEX string. |

**Return Value**

| Type | Description |
| --- | --- |
| String | The HEX string. |

**Examples**

```javascript
> caver.utils.utf8ToHex('I have 100€');
"0x49206861766520313030e282ac"
```


### asciiToHex

```javascript
caver.utils.asciiToHex(string)
```

Returns the HEX representation of a given ASCII string.


**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| string | String | An ASCII string to convert to a HEX string. |

**Return Value**

| Type | Description |
| --- | --- |
| String | The HEX string. |

**Examples**

```javascript
> caver.utils.asciiToHex('I have 100!');
"0x4920686176652031303021"
```

### hexToBytes

```javascript
caver.utils.hexToBytes(hex)
```
Returns a byte array from the given HEX string.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| hex | HEX String | A HEX string to be converted. |

**Return Value**

| Type | Description |
| --- | --- |
| Array | The byte array. |

**Examples**

```javascript
> caver.utils.hexToBytes('0x000000ea');
[ 0, 0, 0, 234 ]
```


### bytesToHex

```javascript
caver.utils.bytesToHex(byteArray)
```
Returns a HEX string from a byte array.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| byteArray | Array| A byte array to convert. |

**Return Value**

| Type | Description |
| --- | --- |
| String | The HEX string. |

**Examples**

```javascript
> caver.utils.bytesToHex([ 72, 101, 108, 108, 111, 33, 36 ]);
"0x48656c6c6f2125"
```

### toPeb

```javascript
caver.utils.toPeb(number [, unit])
```

Converts any KLAY value into peb.

**NOTE**: "peb" is the smallest KLAY unit, and you should always make calculations in peb and convert only for display reasons.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| number | String &#124; Number &#124; BN | The value. |
| unit | String | (optional, defaults to ``"KLAY"``) KLAY to convert from. Possible units are:<br>- ``peb``: '1' <br> - ``kpeb``: '1000' <br> - ``Mpeb``: '1000000' <br> - ``Gpeb``: '1000000000' <br> - ``Ston``: '1000000000' <br> - ``uKLAY``: '1000000000000' <br> - ``mKLAY``: '1000000000000000' <br> - ``KLAY``: '1000000000000000000' <br> - ``kKLAY``: '1000000000000000000000' <br> - ``MKLAY``: '1000000000000000000000000' <br> - ``GKLAY``: '1000000000000000000000000000' <br> |

**Return Value**

| Type | Description |
| --- | --- |
| String &#124; BN | If a number or a string is given, it returns a number string, otherwise a [BN.js](https://github.com/indutny/bn.js/) instance. |

**Examples**

```javascript
> caver.utils.toPeb('1', 'KLAY');
"1000000000000000000"
```


### fromPeb

```javascript
caver.utils.fromPeb(number [, unit])
```

**NOTE**: "peb" is the smallest KLAY unit, and you should always make calculations in KLAY and convert only for display reasons.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| number | String &#124; Number &#124; BN | The value in peb. |
| unit | String | (optional, defaults to ``"KLAY"``) KLAY to convert to. Possible units are:<br>- ``peb``: '1' <br> - ``kpeb``: '1000' <br> - ``Mpeb``: '1000000' <br> - ``Gpeb``: '1000000000' <br> - ``Ston``: '1000000000' <br> - ``uKLAY``: '1000000000000' <br> - ``mKLAY``: '1000000000000000' <br> - ``KLAY``: '1000000000000000000' <br> - ``kKLAY``: '1000000000000000000000' <br> - ``MKLAY``: '1000000000000000000000000' <br> - ``GKLAY``: '1000000000000000000000000000' <br> |

**Return Value**

| Type | Description |
| --- | --- |
| String &#124; BN | If a number or a string is given, it returns a number string, otherwise a [BN.js](https://github.com/indutny/bn.js/) instance. |

**Examples**

```javascript
> caver.utils.fromPeb('1', 'KLAY');
"0.000000000000000001"
```

### unitMap

```javascript
caver.utils.unitMap
```

Shows all possible KLAY values and their amount in peb.

**Return Value**

| Type | Description |
| --- | --- |
| Object | With the following properties:<br>- ``peb``: '1' <br> - ``kpeb``: '1000' <br> - ``Mpeb``: '1000000' <br> - ``Gpeb``: '1000000000' <br> - ``Ston``: '1000000000' <br> - ``uKLAY``: '1000000000000' <br> - ``mKLAY``: '1000000000000000' <br> - ``KLAY``: '1000000000000000000' <br> - ``kKLAY``: '1000000000000000000000' <br> - ``MKLAY``: '1000000000000000000000000' <br> - ``GKLAY``: '1000000000000000000000000000' <br> |


**Examples**

```javascript
> caver.utils.unitMap


{
  peb: '1',
  kpeb: '1000',
  Mpeb: '1000000',
  Gpeb: '1000000000',
  Ston: '1000000000',
  uKLAY: '1000000000000',
  mKLAY: '1000000000000000',
  KLAY: '1000000000000000000',
  kKLAY: '1000000000000000000000',
  MKLAY: '1000000000000000000000000',
  GKLAY: '1000000000000000000000000000',
}
```

### padLeft

```javascript
caver.utils.padLeft(string, characterAmount [, sign])
caver.utils.leftPad(string, characterAmount [, sign]) // ALIAS
```

Adds a padding on the left of a string. Useful for adding paddings to HEX strings.


**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| string | String | The string to add padding on the left.|
| characterAmount | Number | The number of characters the total string should have. |
| sign | String | (optional) The character sign to use, defaults to ``"0"``. |

**Return Value**

| Type | Description |
| --- | --- |
| String | The padded string. |

**Examples**

```javascript
> caver.utils.padLeft('0x3456ff', 20);
"0x000000000000003456ff"

> caver.utils.padLeft(0x3456ff, 20);
"0x000000000000003456ff"

> caver.utils.padLeft('Hello', 20, 'x');
"xxxxxxxxxxxxxxxHello"
```

### padRight

```javascript
caver.utils.padRight(string, characterAmount [, sign])
caver.utils.rightPad(string, characterAmount [, sign]) // ALIAS
```
Adds a padding on the right of a string, Useful for adding paddings to HEX strings.


**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| string | String | The string to add padding on the right. |
| characterAmount | Number | The number of characters the total string should have. |
| sign | String | (optional) The character sign to use, defaults to ``"0"``. |

**Return Value**

| Type | Description |
| --- | --- |
| String | The padded string. |

**Examples**

```javascript
> caver.utils.padRight('0x3456ff', 20);
"0x3456ff00000000000000"

> caver.utils.padRight(0x3456ff, 20);
"0x3456ff00000000000000"

> caver.utils.padRight('Hello', 20, 'x');
"Helloxxxxxxxxxxxxxxx"
```

### toTwosComplement

```javascript
caver.utils.toTwosComplement(number)
```

Converts a negative numer into a two's complement.


**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| number | Number &#124; String &#124; BigNumber | The number to convert. |

**Return Value**

| Type | Description |
| --- | --- |
| String | The converted hex string. |

**Examples**

```javascript
> caver.utils.toTwosComplement('-1');
"0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff"

> caver.utils.toTwosComplement(-1);
"0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff"

> caver.utils.toTwosComplement('0x1');
"0x0000000000000000000000000000000000000000000000000000000000000001"

> caver.utils.toTwosComplement(-15);
"0xfffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff1"

> caver.utils.toTwosComplement('-0x1');
"0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff"
```


## caver.klay.accounts

`caver.klay.accounts` contains functions to generate Klaytn accounts and sign transactions and data.


### create

```javascript
caver.klay.accounts.create([entropy])
```
Generates an account object with private key and public key.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| entropy | String | (optional) A random string to increase entropy. If none is given, a random string will be generated using [randomHex](#randomhex). |


**Return Value**

``Object`` - The account object with the following structure:

| Name | Type | Description |
| --- | --- | --- |
| address | String | The account address. |
| privateKey | String | The accounts private key. This should never be shared or stored unencrypted in local storage! Also make sure to null the memory after usage. |
| signTransaction(tx [, callback]) | Function | The function to sign transactions. See [caver.klay.accounts.signTransaction](#signtransaction-1). |
| sign(data) | Function | The function to sign transactions. See [caver.klay.accounts.sign](#sign). |
| encrypt | Function | The function to encrypt private key with given password. |

**Example**

```javascript
> caver.klay.accounts.create();
{
    address: "0xb8CE9ab6943e0eCED004cDe8e3bBed6568B2Fa01",
    privateKey: "0x348ce564d427a3311b6536bbcff9390d69395b06ed6c486954e971d960fe8709",
    signTransaction: function(tx){...},
    sign: function(data){...},
    encrypt: function(password){...}
}

> caver.klay.accounts.create('2435@#@#@±±±±!!!!678543213456764321§34567543213456785432134567');
{
    address: "0xF2CD2AA0c7926743B1D4310b2BC984a0a453c3d4",
    privateKey: "0xd7325de5c2c1cf0009fac77d3d04a9c004b038883446b065871bc3e831dcd098",
    signTransaction: function(tx){...},
    sign: function(data){...},
    encrypt: function(password){...}
}

> caver.klay.accounts.create(caver.utils.randomHex(32));
{
    address: "0xe78150FaCD36E8EB00291e251424a0515AA1FF05",
    privateKey: "0xcc505ee6067fba3f6fc2050643379e190e087aeffe5d958ab9f2f3ed3800fa4e",
    signTransaction: function(tx){...},
    sign: function(data){...},
    encrypt: function(password){...}
}
```

### privateKeyToAccount

```javascript
caver.klay.accounts.privateKeyToAccount(privateKey)
```
Creates an account object from a private key.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| privateKey | string | The private key to convert. |


**Return Value**

``Object`` - The account object

**Example**

```javascript
> caver.klay.accounts.privateKeyToAccount('0x348ce564d427a3311b6536bbcff9390d69395b06ed6c486954e971d960fe8709');
{
    address: '0xb8CE9ab6943e0eCED004cDe8e3bBed6568B2Fa01',
    privateKey: '0x348ce564d427a3311b6536bbcff9390d69395b06ed6c486954e971d960fe8709',
    signTransaction: function(tx){...},
    sign: function(data){...},
    encrypt: function(password){...}
}

> caver.klay.accounts.privateKeyToAccount('0x348ce564d427a3311b6536bbcff9390d69395b06ed6c486954e971d960fe8709');
{
    address: '0xb8CE9ab6943e0eCED004cDe8e3bBed6568B2Fa01',
    privateKey: '0x348ce564d427a3311b6536bbcff9390d69395b06ed6c486954e971d960fe8709',
    signTransaction: function(tx){...},
    sign: function(data){...},
    encrypt: function(password){...}
}
```


### signTransaction

```javascript
caver.klay.accounts.signTransaction(tx, privateKey [, callback])
```
Signs a Klaytn transaction with a given private key.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| tx | Object | The transaction object.  See the table below for the details. |
| privateKey | String | The private key to sign with. |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

The transaction object contains the following:

| Name | Type | Description |
| --- | --- | --- |
| nonce | String | (optional) The nonce to use when signing this transaction. Default will use [caver.klay.getTransactionCount](#gettransactioncount). |
| chainId | String | (optional) The chain id to use when signing this transaction. Default will use [caver.klay.net.getId](#getid). |
| to | String | (optional) The recevier of the transaction, can be empty when deploying a contract. |
| data | String | (optional) The call data of the transaction, can be empty for simple value transfers. |
| value | String | (optional) The value of the transaction in peb. |
| gasPrice | String | (optional) The gas price set by this transaction. If empty, it will use [caver.klay.getGasPrice](#getgasprice). |
| gas | String | The gas provided by the transaction. |

**Return Value**

``Promise`` returning ``Object``: The signed data RLP encoded transaction, or if ``returnSignature`` is ``true`` the signature values as follows:

| Name | Type | Description |
| --- | --- | --- |
| messageHash | String | The hash of the given message. |
| r | String | First 32 bytes of the signature. |
| s | String | Next 32 bytes of the signature. |
| v | String | Recovery value + 27. |
| rawTransaction | String | The RLP encoded transaction, ready to be send using caver.klay.sendSignedTransaction. |


**Example**

```javascript
> caver.klay.accounts.signTransaction({
      to: '0xF0109fC8DF283027b6285cc889F5aA624EaC1F55',
      value: '1000000000',
      gas: 2000000
  }, '0x4c0883a69102937d6231471b5dbb6204fe5129617082792ae468d01a3f362318')
  .then(console.log);
 {
      messageHash: '0x88cfbd7e51c7a40540b233cf68b62ad1df3e92462f1c6018d6d67eae0f3b08f5',
      v: '0x25',
      r: '0xc9cf86333bcb065d140032ecaab5d9281bde80f21b9687b3e94161de42d51895',
      s: '0x727a108a0b8d101465414033c3f705a9c7b826e596766046ee1183dbc8aeaa68',
      rawTransaction: '0xf869808504e3b29200831e848094f0109fc8df283027b6285cc889f5aa624eac1f55843b9aca008025a0c9cf86333bcb065d140032ecaab5d9281bde80f21b9687b3e94161de42d51895a0727a108a0b8d101465414033c3f705a9c7b826e596766046ee1183dbc8aeaa68'
 }

> caver.klay.accounts.signTransaction({
      to: '0xF0109fC8DF283027b6285cc889F5aA624EaC1F55',
      value: '1000000000',
      gas: 2000000,
      gasPrice: '234567897654321',
      nonce: 0,
      chainId: 1
  }, '0x4c0883a69102937d6231471b5dbb6204fe5129617082792ae468d01a3f362318')
  .then(console.log);
  {
      messageHash: '0x6893a6ee8df79b0f5d64a180cd1ef35d030f3e296a5361cf04d02ce720d32ec5',
      r: '0x9ebb6ca057a0535d6186462bc0b465b561c94a295bdb0621fc19208ab149a9c',
      s: '0x440ffd775ce91a833ab410777204d5341a6f9fa91216a6f3ee2c051fea6a0428',
      v: '0x25',
      rawTransaction: '0xf86a8086d55698372431831e848094f0109fc8df283027b6285cc889f5aa624eac1f55843b9aca008025a009ebb6ca057a0535d6186462bc0b465b561c94a295bdb0621fc19208ab149a9ca0440ffd775ce91a833ab410777204d5341a6f9fa91216a6f3ee2c051fea6a0428'
  }
```


### recoverTransaction

```javascript
caver.klay.accounts.recoverTransaction(rawTransaction)
```
Recovers the Klaytn address that was used to sign the given RLP encoded transaction.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| signature | String | The RLP encoded transaction. |

**Return Value**

| Type | Description |
| --- | --- |
| String | The Klaytn address used to sign this transaction. |

**Example**

```js
> caver.klay.accounts.recoverTransaction('0xf86180808401ef364594f0109fc8df283027b6285cc889f5aa624eac1f5580801ca031573280d608f75137e33fc14655f097867d691d5c4c44ebe5ae186070ac3d5ea0524410802cdc025034daefcdfa08e7d2ee3f0b9d9ae184b2001fe0aff07603d9');
"0xF0109fC8DF283027b6285cc889F5aA624EaC1F55"
```


### hashMessage

```javascript
caver.klay.accounts.hashMessage(message)
```

Hashes the given message in order for it to be passed to [caver.klay.accounts.recover](#recover).
The data will be UTF-8 HEX decoded and enveloped as follows:
```
"\x19Klaytn Signed Message:\n" + message.length + message
```
and hashed using keccak256.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| message | String | A message to hash.  If it is a HEX string, it will be UTF-8 decoded first. |


**Return Value**

| Type | Description |
| --- | --- |
| String | The hashed message |


**Example**

```javascript
> caver.klay.accounts.hashMessage("Hello World")
"0xf334bf277b674260e85f1a3d2565d76463d63d29549ef4fa6d6833207576b5ba"

// the below results in the same hash
> caver.klay.accounts.hashMessage(caver.utils.utf8ToHex("Hello World"))
"0xf334bf277b674260e85f1a3d2565d76463d63d29549ef4fa6d6833207576b5ba"
```


### sign

```javascript
caver.klay.accounts.sign(data, privateKey)
```
Signs arbitrary data. This data is before UTF-8 HEX decoded and enveloped as follows:
```
"\x19Klaytn Signed Message:\n" + message.length + message
```

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| data | String | The data to sign. |
| privateKey | String | The private key to sign with. |


**Return Value**

``String|Object``: The signed data RLP encoded signature. The signature values as follows:

| Name | Type | Description |
| --- | --- | --- |
| message | String | The given message. |
| messageHash | String | The hash of the given message. |
| r | String | First 32 bytes of the signature. |
| s | String | Next 32 bytes of the signature. |
| v | String | Recovery value + 27 |
| signature | String | The generated signature. |


**Example**

```javascript
> caver.klay.accounts.sign('Some data', '0x4c0883a69102937d6231471b5dbb6204fe5129617082792ae468d01a3f362318');
{
    message: 'Some data',
    messageHash: '0x8ed2036502ed7f485b81feaec1c581d236a8b711e55a24077724879c8a263c2a',
    v: '0x1b',
    r: '0x4a57bcff1637346a4323a67acd7a478514d9f00576f42942d50a5ca0e4b0342b',
    s: '0x5914e19a8ebc10ce1450b00a3b9c1bf0ce01909bca3ffdead1aa3a791a97b5ac',
    signature: '0x4a57bcff1637346a4323a67acd7a478514d9f00576f42942d50a5ca0e4b0342b5914e19a8ebc10ce1450b00a3b9c1bf0ce01909bca3ffdead1aa3a791a97b5ac1b'
}
```


### recover

```javascript
caver.klay.accounts.recover(signatureObject)
caver.klay.accounts.recover(message, signature [, preFixed])
caver.klay.accounts.recover(message, v, r, s [, preFixed])
```
Recovers the Klaytn address that was used to sign the given data.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| message &#124; signatureObject | String &#124; Object | Either signed message or hash. For the details of the signature object, see the table below. |
| messageHash | String | The hash of the given message. |
| signature | String | The raw RLP encoded signature, OR parameter 2-4 as v, r, s values. |
| preFixed | Boolean | (optional, default: ``false``) If the last parameter is ``true``, the given message will NOT automatically be prefixed with ``"\x19Klaytn Signed Message:\n" + message.length + message``, and assumed to be already prefixed. |

The signature object has following values:

| Name | Type | Description |
| --- | --- | --- |
| messageHash | String | The hash of the given message already prefixed with `"\x19Klaytn Signed Message:\n" + message.length + message`. |
| r | String | First 32 bytes of the signature. |
| s | String | Next 32 bytes of the signature. |
| v | String | Recovery value + 27 |


**Return Value**

| Type | Description |
| --- | --- |
| String | The Klaytn address used to sign this data. |


**Example**

```javascript
> caver.klay.accounts.recover({
      messageHash: '0x8ed2036502ed7f485b81feaec1c581d236a8b711e55a24077724879c8a263c2a',
      v: '0x1b',
      r: '0x4a57bcff1637346a4323a67acd7a478514d9f00576f42942d50a5ca0e4b0342b',
      s: '0x5914e19a8ebc10ce1450b00a3b9c1bf0ce01909bca3ffdead1aa3a791a97b5ac',
  })
"0x2c7536E3605D9C16a7a3D7b1898e529396a65c23"

// message, signature
> caver.klay.accounts.recover('Some data', '0x4a57bcff1637346a4323a67acd7a478514d9f00576f42942d50a5ca0e4b0342b5914e19a8ebc10ce1450b00a3b9c1bf0ce01909bca3ffdead1aa3a791a97b5ac1b');
"0x2c7536E3605D9C16a7a3D7b1898e529396a65c23"

// message, v, r, s
> caver.klay.accounts.recover('Some data', '0x1b', '0x4a57bcff1637346a4323a67acd7a478514d9f00576f42942d50a5ca0e4b0342b', '0x5914e19a8ebc10ce1450b00a3b9c1bf0ce01909bca3ffdead1aa3a791a97b5ac');
"0x2c7536E3605D9C16a7a3D7b1898e529396a65c23"
```


### encrypt

```javascript
caver.klay.accounts.encrypt(privateKey, password)
```
Encrypts a private key to the Klaytn keystore v3 standard.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| privateKey | String | The private key to encrypt. |
| password | String | The password used for encryption. |


**Return Value**

| Type | Description |
| --- | --- |
| Object | The encrypted keystore v3 JSON. |


**Example**

```javascript
> caver.klay.accounts.encrypt('0x4c0883a69102937d6231471b5dbb6204fe5129617082792ae468d01a3f362318', 'test!');
{
    version: 3,
    id: '04e9bcbb-96fa-497b-94d1-14df4cd20af6',
    address: '2c7536e3605d9c16a7a3d7b1898e529396a65c23',
    crypto: {
        ciphertext: 'a1c25da3ecde4e6a24f3697251dd15d6208520efc84ad97397e906e6df24d251',
        cipherparams: { iv: '2885df2b63f7ef247d753c82fa20038a' },
        cipher: 'aes-128-ctr',
        kdf: 'scrypt',
        kdfparams: {
            dklen: 32,
            salt: '4531b3c174cc3ff32a6a7a85d6761b410db674807b2d216d022318ceee50be10',
            n: 262144,
            r: 8,
            p: 1
        },
        mac: 'b8b010fff37f9ae5559a352a185e86f9b9c1d7f7a9f1bd4e82a5dd35468fc7f6'
    }
}
```


### decrypt

```javascript
caver.klay.accounts.decrypt(keystoreJsonV3, password)
```
Decrypts a keystore v3 JSON and returns the decrypted account object.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| keystoreJsonV3 | String | JSON string containing the encrypted private key to decrypt. |
| password | String | The password used for encryption. |


**Return Value**

| Type | Description |
| --- | --- |
| Object | The decrypted account. |


**Example**

```javascript
> caver.klay.accounts.decrypt({
     version: 3,
     id: '04e9bcbb-96fa-497b-94d1-14df4cd20af6',
     address: '2c7536e3605d9c16a7a3d7b1898e529396a65c23',
     crypto: {
         ciphertext: 'a1c25da3ecde4e6a24f3697251dd15d6208520efc84ad97397e906e6df24d251',
         cipherparams: { iv: '2885df2b63f7ef247d753c82fa20038a' },
         cipher: 'aes-128-ctr',
         kdf: 'scrypt',
         kdfparams: {
             dklen: 32,
             salt: '4531b3c174cc3ff32a6a7a85d6761b410db674807b2d216d022318ceee50be10',
             n: 262144,
             r: 8,
             p: 1
         },
         mac: 'b8b010fff37f9ae5559a352a185e86f9b9c1d7f7a9f1bd4e82a5dd35468fc7f6'
     }
  }, 'test!');
{
    address: "0x2c7536E3605D9C16a7a3D7b1898e529396a65c23",
    privateKey: "0x4c0883a69102937d6231471b5dbb6204fe5129617082792ae468d01a3f362318",
    signTransaction: function(tx){...},
    sign: function(data){...},
    encrypt: function(password){...}
}
```


### wallet

```javascript
caver.klay.accounts.wallet
```
Contains an in-memory wallet with multiple accounts.  These accounts can be used
when using [caver.klay.sendTransaction](#sendtransaction).

**Example**

```javascript
> caver.klay.accounts.wallet;
Wallet {
    0: {...}, // account by index
    "0xF0109fC8DF283027b6285cc889F5aA624EaC1F55": {...},  // same account by address
    "0xf0109fc8df283027b6285cc889f5aa624eac1f55": {...},  // same account by address lowercase
    1: {...},
    "0xD0122fC8DF283027b6285cc889F5aA624EaC1d23": {...},
    "0xd0122fc8df283027b6285cc889f5aa624eac1d23": {...},

    add: function(){},
    remove: function(){},
    save: function(){},
    load: function(){},
    clear: function(){},

    length: 2,
}
```


### wallet.create

```javascript
caver.klay.accounts.wallet.create(numberOfAccounts [, entropy])
```
Generates one or more accounts in the wallet. If wallets already exist, they will not be overridden.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| numberOfAccounts | Number | The number of accounts to create. Leave empty to create an empty wallet. |
| entropy | String | (optional) A random string to increase entropy. If none is given, a random string will be generated using [randomHex](#randomhex). |

**Return Value**

| Type | Description |
| --- | --- |
| Object | The wallet object. |


**Example**

```javascript
> caver.klay.accounts.wallet.create(2, '54674321§3456764321§345674321§3453647544±±±§±±±!!!43534534534534');
Wallet {
    0: {...},
    "0xF0109fC8DF283027b6285cc889F5aA624EaC1F55": {...},
    "0xf0109fc8df283027b6285cc889f5aa624eac1f55": {...},
    ...
}
```


### wallet.add

```javascript
caver.klay.accounts.wallet.add(account)
```
Adds an account using a private key or account object to the wallet.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| account | String &#124; Object | A private key or account object created with [caver.klay.accounts.create](#create). |


**Return Value**

| Type | Description |
| --- | --- |
| Object | The added account. |


**Example**

```javascript
> caver.klay.accounts.wallet.add('0x4c0883a69102937d6231471b5dbb6204fe5129617082792ae468d01a3f362318');
{
    index: 0,
    address: '0x2c7536E3605D9C16a7a3D7b1898e529396a65c23',
    privateKey: '0x4c0883a69102937d6231471b5dbb6204fe5129617082792ae468d01a3f362318',
    signTransaction: function(tx){...},
    sign: function(data){...},
    encrypt: function(password){...}
}

> caver.klay.accounts.wallet.add({
      privateKey: '0x348ce564d427a3311b6536bbcff9390d69395b06ed6c486954e971d960fe8709',
      address: '0xb8CE9ab6943e0eCED004cDe8e3bBed6568B2Fa01'
  });
{
    index: 0,
    address: '0xb8CE9ab6943e0eCED004cDe8e3bBed6568B2Fa01',
    privateKey: '0x348ce564d427a3311b6536bbcff9390d69395b06ed6c486954e971d960fe8709',
    signTransaction: function(tx){...},
    sign: function(data){...},
    encrypt: function(password){...}
}
```


### wallet.remove

```javascript
caver.klay.accounts.wallet.remove(account)
```
Removes an account from the wallet.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| account | String &#124; Number | The account address or the index in the wallet. |


**Return Value**

| Type | Description |
| --- | --- |
| Boolean | ``true`` if the wallet was removed. ``false`` if it could not be found. |


**Example**

```javascript
> caver.klay.accounts.wallet;
Wallet {
    0: {...},
    "0xF0109fC8DF283027b6285cc889F5aA624EaC1F55": {...}
    1: {...},
    "0xb8CE9ab6943e0eCED004cDe8e3bBed6568B2Fa01": {...}
    ...
}

> caver.klay.accounts.wallet.remove('0xF0109fC8DF283027b6285cc889F5aA624EaC1F55');
true

> caver.klay.accounts.wallet.remove(3);
false
```


### wallet.clear

```javascript
caver.klay.accounts.wallet.clear()
```
Securely empties the wallet and removes all its accounts.

**Parameters**

None

**Return Value**

| Type | Description |
| --- | --- |
| Object | The wallet object. |

**Example**

```javascript
> caver.klay.accounts.wallet.clear();
Wallet {
    add: function(){},
    remove: function(){},
    save: function(){},
    load: function(){},
    clear: function(){},

    length: 0
}
```


### wallet.encrypt

```javascript
caver.klay.accounts.wallet.encrypt(password)
```
Encrypts all wallet accounts and returns an array of encrypted keystore v3 objects.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| password | String | The password that will be used for encryption. |


**Return Value**

| Type | Description |
| --- | --- |
| Array | The encrypted keystore v3 objects. |


**Example**

```javascript
> caver.klay.accounts.wallet.encrypt('test');
[ { version: 3,
      id: 'dcf8ab05-a314-4e37-b972-bf9b86f91372',
      address: '06f702337909c06c82b09b7a22f0a2f0855d1f68',
      crypto:
       { ciphertext: '0de804dc63940820f6b3334e5a4bfc8214e27fb30bb7e9b7b74b25cd7eb5c604',
         cipherparams: [Object],
         cipher: 'aes-128-ctr',
         kdf: 'scrypt',
         kdfparams: [Object],
         mac: 'b2aac1485bd6ee1928665642bf8eae9ddfbc039c3a673658933d320bac6952e3'
       }
  },
  { version: 3,
      id: '9e1c7d24-b919-4428-b10e-0f3ef79f7cf0',
      address: 'b5d89661b59a9af0b34f58d19138baa2de48baaf',
      crypto:
       { ciphertext: 'd705ebed2a136d9e4db7e5ae70ed1f69d6a57370d5fbe06281eb07615f404410',
         cipherparams: [Object],
         cipher: 'aes-128-ctr',
         kdf: 'scrypt',
         kdfparams: [Object],
         mac: 'af9eca5eb01b0f70e909f824f0e7cdb90c350a802f04a9f6afe056602b92272b' }
  }
]
```


### wallet.decrypt

```javascript
caver.klay.accounts.wallet.decrypt(keystoreArray, password)
```
Decrypts keystore v3 objects.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| keystoreArray | Array | The encrypted keystore v3 objects to decrypt. |
| password | String | The password that was used for encryption. |


**Return Value**

| Type | Description |
| --- | --- |
| Object | The wallet object. |


**Example**

```javascript
> caver.klay.accounts.wallet.decrypt([
    { version: 3,
      id: '83191a81-aaca-451f-b63d-0c5f3b849289',
      address: '06f702337909c06c82b09b7a22f0a2f0855d1f68',
      crypto:
       { ciphertext: '7d34deae112841fba86e3e6cf08f5398dda323a8e4d29332621534e2c4069e8d',
         cipherparams: { iv: '497f4d26997a84d570778eae874b2333' },
         cipher: 'aes-128-ctr',
         kdf: 'scrypt',
         kdfparams:
          { dklen: 32,
            salt: '208dd732a27aa4803bb760228dff18515d5313fd085bbce60594a3919ae2d88d',
            n: 262144,
            r: 8,
            p: 1 },
         mac: '0062a853de302513c57bfe3108ab493733034bf3cb313326f42cf26ea2619cf9' }
        },
       { version: 3,
      id: '7d6b91fa-3611-407b-b16b-396efb28f97e',
      address: 'b5d89661b59a9af0b34f58d19138baa2de48baaf',
      crypto:
       { ciphertext: 'cb9712d1982ff89f571fa5dbef447f14b7e5f142232bd2a913aac833730eeb43',
         cipherparams: { iv: '8cccb91cb84e435437f7282ec2ffd2db' },
         cipher: 'aes-128-ctr',
         kdf: 'scrypt',
         kdfparams:
          { dklen: 32,
            salt: '08ba6736363c5586434cd5b895e6fe41ea7db4785bd9b901dedce77a1514e8b8',
            n: 262144,
            r: 8,
            p: 1
          },
         mac: 'd2eb068b37e2df55f56fa97a2bf4f55e072bef0dd703bfd917717d9dc54510f0' }
       }
    ], 'test');
Wallet {
    0: {...},
    1: {...},
    "0xF0109fC8DF283027b6285cc889F5aA624EaC1F55": {...},
    "0xD0122fC8DF283027b6285cc889F5aA624EaC1d23": {...}
    ...
}
```


## caver.klay.Contract

The ``caver.klay.Contract`` object makes it easy to interact with smart
contracts on the Klaytn blockchain.  When you create a new contract object, you
give it the JSON interface of the respective smart contract and caver will auto
convert all calls into low level ABI calls over RPC for you.

This allows you to interact with smart contracts as if they were JavaScript objects.

To use it standalone:

```javascript
var Contract = require('caver-klay-contract');

// set provider for all later instances to use
Contract.setProvider('ws://localhost:8546');

var contract = new Contract(jsonInterface, address);

contract.methods.somFunc().send({from: ....})
   .on('receipt', function() {
       ...
});
```


### new contract

```javascript
new caver.klay.Contract(jsonInterface [, address] [, options])
```
Creates a new contract instance with all its methods and events defined in its JSON interface object.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| jsonInterface | Object | The JSON interface for the contract to instantiate |
| address | String | (optional) The address of the smart contract to call. Can be added later using ``myContract.options.address = '0x1234..'`` |
| options | Object | (optional) The options of the contract.  See the table below for the details. |

The options object contains the following:

| Name | Type | Description |
| --- | --- | --- |
| from | String | (optional) The address from which transactions should be made. |
| gasPrice | String | (optional) The gas price in peb to use for transactions. |
| gas | Number | (optional) The maximum gas provided for a transaction (gas limit). |
| data | String | (optional) The byte code of the contract. Used when the contract gets deployed. |


**Return Value**

| Type | Description |
| --- | --- |
| Object | The contract instance with all its methods and events. |


**Example**

```javascript
var myContract = new caver.klay.Contract([...], '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe', {
      from: '0x1234567890123456789012345678901234567891', // default from address
      gasPrice: '25000000000' // default gas price in peb, 25 Gpeb in this case
});
```


### options

```javascript
myContract.options
```
The `options` object for the contract instance. `from`, `gas` and
`gasPrice` are used as fallback values when sending transactions.

**Properties**

| Name | Type | Description |
| --- | --- | --- |
| address | String | The address where the contract is deployed.  Also see [options.address](#optionsaddress). |
| jsonInterface | Array | The JSON interface of the contract.  Also see [options.jsonInterface](#optionsjsoninterface). |
| data | String | The byte code of the contract. Used when the contract gets deployed. |
| from | String | The address from which transactions should be made. |
| gasPrice | String | The gas price in peb to use for transactions. |
| gas | Number | The maximum gas provided for a transaction (gas limit). |


**Example**

```javascript
> myContract.options;
{
    address: '0x1234567890123456789012345678901234567891',
    jsonInterface: [...],
    from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe',
    gasPrice: '10000000000000',
    gas: 1000000
}

> myContract.options.from = '0x1234567890123456789012345678901234567891'; // default from address
> myContract.options.gasPrice = '25000000000000'; // default gas price in peb
> myContract.options.gas = 5000000; // provide as fallback always 5M gas
```


### options.address

```javascript
myContract.options.address
```
The address used for this contract instance `myContract`.  All transactions
generated by `caver.js` from this contract will contain this address as the
"to".  The address is stored in lowercase.

**Property**

| Name | Type | Description |
| --- | --- | --- |
| address | String &#124; `null` | The address for this contract or ``null`` if it is not yet set. |

**Example**

```javascript
>  myContract.options.address;
'0xde0b295669a9fd93d5f28d9ec85e40f4cb697bae'

// set a new address
>  myContract.options.address = '0x1234FFDD...';
```


### options.jsonInterface

```javascript
myContract.options.jsonInterface
```
The JSON interface object derived from the ABI of this contract `myContract`.

**Property**

| Name | Type | Description |
| --- | --- | --- |
| jsonInterface | Array | The JSON interface for this contract. Re-setting this will regenerate the methods and events of the contract instance. |


**Example**

```javascript
> myContract.options.jsonInterface;
[{
      "type":"function",
      "name":"foo",
      "inputs": [{"name":"a","type":"uint256"}],
      "outputs": [{"name":"b","type":"address"}]
 },{
      "type":"event",
      "name":"Event"
      "inputs": [{"name":"a","type":"uint256","indexed":true},{"name":"b","type":"bytes32","indexed":false}],
 }]

// set a new interface
> myContract.options.jsonInterface = [...];
```


### clone

```javascript
myContract.clone()
```
Clones the current contract instance.

**Parameters**

None

**Return Value**

| Type | Description |
| --- | --- |
| Object | The new cloned contract instance. |


**Example**

```javascript
> var contract1 = new caver.klay.Contract(abi, address, {gasPrice: '12345678', from: fromAddress});
> var contract2 = contract1.clone();
> contract2.options.address = address2;
> (contract1.options.address !== contract2.options.address);
true
```


### deploy

```javascript
myContract.deploy(options)
```
Deploys the contract to the Klaytn blockchain.  After successful deployment,
the promise will resolve with a new contract instance.

**Parameters**

`options`: the options object used for deployment:

| Name | Type | Description |
| --- | --- | --- |
| data | String | The byte code of the contract. |
| arguments | Array | (optional) The arguments that get passed to the constructor on deployment. |

**Return Value**

`Object`: The transaction object:

| Type | Description |
| --- | --- |
| Array | arguments: The arguments passed to the method before. They can be changed. |
| Function | [send](#methodsmymethodsend): Will deploy the contract. The promise will resolve with the new contract instance, instead of the receipt. |
| Function | [estimateGas](#methodsmymethodestimategas): Will estimate the gas used for the deployment. |
| Function | [encodeABI](#methodsmymethodencodeabi): Encodes the ABI of the deployment, which is contract data + constructor parameters. |

**Example**

```javascript
> myContract.deploy({
      data: '0x12345...',
      arguments: [123, 'My String']
  })
  .send({
      from: '0x1234567890123456789012345678901234567891',
      gas: 1500000,
      gasPrice: '25000000000'
  }, function(error, transactionHash) { ... })
  .on('error', function(error) { ... })
  .on('transactionHash', function(transactionHash) { ... })
  .on('receipt', function(receipt) {
     console.log(receipt.contractAddress) // contains the new contract address
   })
  .then(function(newContractInstance) {
      console.log(newContractInstance.options.address) // instance with the new contract address
  });

// When the data is already set as an option to the contract itself
> myContract.options.data = '0x12345...';

> myContract.deploy({
        arguments: [123, 'My String']
  })
  .send({
      from: '0x1234567890123456789012345678901234567891',
      gas: 1500000,
      gasPrice: '25000000000'
  })
  .then(function(newContractInstance) {
      console.log(newContractInstance.options.address) // instance with the new contract address
  });

// Simply encoding
> myContract.deploy({
      data: '0x12345...',
      arguments: [123, 'My String']
  })
  .encodeABI();
'0x12345...0000012345678765432'

// Gas estimation
> myContract.deploy({
      data: '0x12345...',
      arguments: [123, 'My String']
  })
  .estimateGas(function(err, gas) {
      console.log(gas);
  });
```


### methods

```javascript
myContract.methods.myMethod([param1 [, param2 [, ...]]])
```
Creates a transaction object for that method, which then can be called, sent, estimated or ABI encoded.

The methods of this smart contract are available through:

- The name: `myContract.methods.myMethod(123)`
- The name with parameters: `myContract.methods['myMethod(uint256)'](123)`
- The signature*: `myContract.methods['0x58cf5f10'](123)`

This allows calling functions with the same name but different parameters from the JavaScript contract object.

#### cf) \*Function signature (Function selector)  
The first four bytes of the call data for a function call specifies the function to be called.  
It is the first (left, high-order in big-endian) four bytes of the Keccak-256 (SHA-3) hash of the signature of the function.

The function signature can be made by 2 different methods.  
`1. caver.klay.abi.encodeFunctionSignature('funcName(funcType1,funcType2,...)')`  
`2. caver.klay.utils.sha3('funcName(funcType1,funcType2,...)').substr(0, 10)`

ex)  
```javascript
caver.klay.abi.encodeFunctionSignature('myMethod(uint256)')
> 0x58cf5f10

caver.klay.utils.sha3('myMethod(uint256)').substr(0, 10)
> 0x58cf5f10
```

**Parameters**

Parameters of any method depend on the smart contracts methods, defined in the JSON interface.

**Return Value**

``Object``: The transaction object:

| Type | Description |
| --- | --- |
| Array | arguments: The arguments passed to the method before. They can be changed. |
| Function | [call](#methodsmymethodcall): Will call the "constant" method and execute its smart contract method in the Klaytn Virtual Machine without sending a transaction (cannot alter the smart contract state). |
| Function | [send](#methodsmymethodsend): Will send a transaction to the smart contract and execute its method (can alter the smart contract state). |
| Function | [estimateGas](#methodsmymethodestimategas): Will estimate the gas used when the method would be executed on the blockchain. |
| Function | [encodeABI](#methodsmymethodencodeabi): Encodes the ABI for this method. This can be sent using a transaction, calling the method or passing into another smart contract method as argument. |

**Example**

```javascript
// calling a method
> myContract.methods.myMethod(123).call({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'}, function(error, result) {
      ...
  });

// or sending and using a promise
> myContract.methods.myMethod(123).send({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'})
  .then(function(receipt) {
    // receipt can also be a new contract instance, when coming from a "contract.deploy({...}).send()"
  });

// or sending and using the events
> myContract.methods.myMethod(123).send({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'})
  .on('transactionHash', function(hash) {
      ...
  })
  .on('receipt', function(receipt) {
      ...
  })
  .on('error', console.error);
```


### methods.myMethod.call

```javascript
myContract.methods.myMethod([param1 [, param2 [, ...]]]).call(options [, callback])
```
Will call a "constant" method and execute its smart contract method in the
Klaytn Virtual Machine without sending any transaction.  Note that calling
cannot alter the smart contract state.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| options | Object | (optional) The options used for calling.  See the table below for the details. |
| callback | Function | (optional) This callback will be fired with the result of the smart contract method execution as the second argument, or with an error object as the first argument. |

The options object can contain the following:

| Name | Type | Description |
| --- | --- | --- |
| from | String | (optional) The address the call “transaction” should be made from. |
| gasPrice | String | (optional) The gas price in peb to use for this call "transaction". |
| gas | Number | (optional) The maximum gas provided for this call "transaction" (gas limit). |

**Return Value**

``Promise`` returns ``Mixed``: The return value(s) of the smart contract method.
If it returns a single value, it is returned as it is.  If it has multiple
return values, they are returned as an object with properties and indices.

**Example**

```javascript
// using the callback
> myContract.methods.myMethod(123).call({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'}, function(error, result) {
      ...
  });

// using the promise
> myContract.methods.myMethod(123).call({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'})
  .then(function(result) {
      ...
  });
```

```solidity
// Solidity: MULTI-ARGUMENT RETURN
contract MyContract {
    function myFunction() returns(uint256 myNumber, string myString) {
        return (23456, "Hello!%");
    }
}
```

```javascript
> var MyContract = new caver.klay.Contract(abi, address);
> MyContract.methods.myFunction().call().then(console.log);
Result {
      myNumber: '23456',
      myString: 'Hello!%',
      0: '23456', // these are here as fallbacks if the name is not known or given
      1: 'Hello!%'
}
```

```solidity
// Solidity: SINGLE-ARGUMENT RETURN
contract MyContract {
    function myFunction() returns(string myString) {
        return "Hello!%";
    }
}
```

```javascript
> var MyContract = new caver.klay.Contract(abi, address);
> MyContract.methods.myFunction().call().then(console.log);
"Hello!%"
```


### methods.myMethod.send

```javascript
myContract.methods.myMethod([param1 [, param2 [, ...]]]).send(options [, callback])
```
Will send a transaction to the smart contract and execute its method.  Note
that this can alter the smart contract state.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| options | Object | The options used for sending.  See the table below for the details. |
| callback | Function | (optional) This callback will be fired first with the "transactionHash", or with an error object as the first argument. |

The options object can contain the following:

| Name | Type | Description |
| --- | --- | --- |
| from | String | The address from which the transaction should be sent. |
| gasPrice | String | (optional) The gas price in peb to use for this transaction. |
| gas | Number | (optional) The maximum gas provided for this transaction (gas limit). |
| value | Number &#124; String &#124; BN &#124; BigNumber | (optional) The value transferred for the transaction in peb. |

**Return Value**

`callback` will return the 32-byte transaction hash.

`PromiEvent`: A promise combined event emitter.  Will be resolved when the
transaction receipt is available, or if this `send()` is called from a
`someContract.deploy()`, then the promise will resolve with the new contract
instance.  Additionally, the following events are available:

| Name | Type | Description |
| --- | --- | --- |
| transactionHash | String | Is fired right after the transaction is sent and a transaction hash is available. |
| receipt | Object | Is fired when the transaction receipt is available.  Receipts from contracts will have no `logs` property, but instead an `events` property with event names as keys and events as properties. See [getPastEvents return values](#getpastevents) for details about the returned event object. |
| error | Error | Is fired if an error occurs during sending. On an out-of-gas error, the second parameter is the receipt. |


**Example**

```javascript
// using the callback
> myContract.methods.myMethod(123).send({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'}, function(error, transactionHash) {
    ...
  });

// using the promise
> myContract.methods.myMethod(123).send({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'})
  .then(function(receipt) {
    // receipt can also be a new contract instance, when coming from a "contract.deploy({...}).send()"
  });


// using the event emitter
> myContract.methods.myMethod(123).send({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'})
  .on('transactionHash', function(hash) {
    ...
  })
  .on('receipt', function(receipt) {
    console.log(receipt);
  })
  .on('error', console.error); // If there is an out-of-gas error, the second parameter is the receipt.

// receipt example
{
   "transactionHash": "0x9fc76417374aa880d4449a1f7f31ec597f00b1f6f3dd2d66f4c9c6c445836d8b",
   "transactionIndex": 0,
   "blockHash": "0xef95f2f1ed3ca60b048b4bf67cde2195961e0bba6f70bcbea9a2c4e133e34b46",
   "blockNumber": 3,
   "contractAddress": "0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe",
   "cumulativeGasUsed": 314159,
   "gasUsed": 30234,
   "events": {
     "MyEvent": {
       returnValues: {
         myIndexedParam: 20,
         myOtherIndexedParam: '0x123456789...',
         myNonIndexParam: 'My String'
       },
       raw: {
         data: '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385',
         topics: ['0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7', '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385']
       },
       event: 'MyEvent',
       signature: '0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7',
       logIndex: 0,
       transactionIndex: 0,
       transactionHash: '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385',
       blockHash: '0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7',
       blockNumber: 1234,
       address: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'
    },
    "MyOtherEvent": {
      ...
    },
    "MyMultipleEvent":[{...}, {...}] // If there are a multiple of the same events, they will be in an array.
  }
}
```


### methods.myMethod.estimateGas

```javascript
myContract.methods.myMethod([param1 [, param2 [, ...]]]).estimateGas(options [, callback])
```
Will estimate the gas that a method execution will take when executed in the
Klaytn Virtual Machine.  The estimation can differ from the actual gas used
when later sending a transaction, as the state of the smart contract can be
different at that time.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| options | Object | (optional) The options used for calling.  See the table below for the details. |
| callback | Function | (optional) This callback will be fired with the result of the gas estimation as the second argument, or with an error object as the first argument. |

The options object can contain the following:

| Name | Type | Description |
| --- | --- | --- |
| from | String | (optional) The address from which the call "transaction" should be made. |
| gas | Number | (optional) The maximum gas provided for this call "transaction" (gas limit). Setting a specific value helps to detect out of gas errors. If all gas is used, it will return the same number. |
| value | Number &#124; String &#124; BN &#124; BigNumber | (optional) The value transferred for the call "transaction" in peb. |

**Return Value**

``Promise`` returns ``Number`` - the used gas for the simulated call/transaction.

**Example**

```javascript
// using the callback
> myContract.methods.myMethod(123).estimateGas({gas: 5000000}, function(error, gasAmount) {
    if(gasAmount == 5000000)
      console.log('Method ran out of gas');
  });

// using the promise
> myContract.methods.myMethod(123).estimateGas({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'})
  .then(function(gasAmount) {
    ...
  })
  .catch(function(error) {
    ...
  });
```


### methods.myMethod.encodeABI

```javascript
myContract.methods.myMethod([param1 [, param2[, ...]]]).encodeABI()
```
Encodes the ABI for this method.  This can be used to send a transaction, call
a method, or pass it into another smart contract method as arguments.


**Parameters**

None

**Return Value**

| Type | Description |
| --- | --- |
| String | The encoded ABI byte code to send via a transaction or call. |


**Example**

```javascript
> myContract.methods.myMethod(123).encodeABI();
'0x58cf5f1000000000000000000000000000000000000000000000000000000000000007B'
```


### once

```javascript
myContract.once(event [, options], callback)
```
Subscribes to an event and unsubscribes immediately after the first event or
error.  Will only fire for a single event.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| event | String | The name of the event in the contract, or ``"allEvents"`` to get all events. |
| options | Object | (optional) The options used for deployment.  See the table below for the details. |
| callback | Function | This callback will be fired for the first event as the second argument, or an error as the first argument. See [getPastEvents return values](#getpastevents) for details about the event structure. |

The options object can contain the following:

| Name | Type | Description |
| --- | --- | --- |
| filter | Object | (optional) Lets you filter events by indexed parameters, *e.g.*, `{filter: {myNumber: [12,13]}}` means all events where "myNumber" is 12 or 13. |
| topics | Array | (optional) This allows you to manually set the topics for the event filter. If given the filter property and event signature, `topic[0]` will not be set automatically. |

**Return Value**

`undefined`

**Example**

```javascript
> myContract.once('MyEvent', {
    filter: {myIndexedParam: [20,23], myOtherIndexedParam: '0x123456789...'}, // Using an array means OR: e.g. 20 or 23
  }, function(error, event) { console.log(event); });

// event output example
{
    returnValues: {
        myIndexedParam: 20,
        myOtherIndexedParam: '0x123456789...',
        myNonIndexParam: 'My String'
    },
    raw: {
        data: '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385',
        topics: ['0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7', '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385']
    },
    event: 'MyEvent',
    signature: '0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7',
    logIndex: 0,
    transactionIndex: 0,
    transactionHash: '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385',
    blockHash: '0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7',
    blockNumber: 1234,
    address: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'
}
```


### events

```javascript
myContract.events.MyEvent([options][, callback])
```
Subscribes to an event.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| options | Object | (optional) The options used for deployment.  See the table below for the details. |
| callback | Function | (optional) This callback will be fired for each event as the second argument, or an error as the first argument. |

The options object can contain the following:

| Name | Type | Description |
| --- | --- | --- |
| filter | Object | (optional) Lets you filter events by indexed parameters, *e.g.*, `{filter: {myNumber: [12,13]}}` means all events where "myNumber" is 12 or 13. |
| fromBlock | Number | (optional) The block number from which to get events on. |
| topics | Array | (optional) This allows to manually set the topics for the event filter. If given the filter property and event signature, `topic[0]` will not be set automatically. |


**Return Value**

``EventEmitter``: The event emitter has the following events:

| Name | Type | Description |
| --- | --- | --- |
| data | Object | Fires on each incoming event with the event object as argument. |
| error | Object | Fires when an error in the subscription occurs. |

The structure of the returned event `Object` looks as follows:

| Name | Type | Description |
| --- | --- | --- |
| event | String | The event name. |
| signature | String &#124; `null` | The event signature, `null` if it is an anonymous event. |
| address | String | Address which from this event originated. |
| returnValues | Object | The return values coming from the event, *e.g.*, `{myVar: 1, myVar2: '0x234...'}`. |
| logIndex | Number | Integer of the event index position in the block. |
| transactionIndex | Number | Integer of the transaction's index position where the event was created. |
| transactionHash | 32-byte String | Hash of the block this event was created in. `null` when it is still pending. |
| blockHash | 32-byte String | Hash of the block this event was created in. `null` when it is still pending. |
| blockNumber | Number | The block number this log was created in. `null` when still pending. |
| raw.data | String | The data containing non-indexed log parameter. |
| raw.topics | Array | An array with max 4 32-byte topics, topic 1-3 contains indexed parameters of the event. |
| id | String | A log identifier. It is made through concatenating "log_" string with `keccak256(blockHash + transactionHash + logIndex).substr(0, 8)` |

**Example**

```javascript
> myContract.events.MyEvent({
    filter: {myIndexedParam: [20,23], myOtherIndexedParam: '0x123456789...'}, // Using an array means OR: e.g. 20 or 23
    fromBlock: 0
  }, function(error, event) { console.log(event); })
  .on('data', function(event){
      console.log(event); // same results as the optional callback above
  })
  .on('error', console.error);

// event output example
{
    returnValues: {
        myIndexedParam: 20,
        myOtherIndexedParam: '0x123456789...',
        myNonIndexParam: 'My String'
    },
    raw: {
        data: '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385',
        topics: ['0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7', '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385']
    },
    event: 'MyEvent',
    signature: '0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7',
    logIndex: 0,
    transactionIndex: 0,
    transactionHash: '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385',
    blockHash: '0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7',
    blockNumber: 1234,
    address: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe',
    id: 'log_41d221bc',
}
```


### events.allEvents

```javascript
myContract.events.allEvents([options] [, callback])
```
Same as [events](#events) but receives all events from this smart contract.
Optionally, the filter property can filter those events.


### getPastEvents

```javascript
myContract.getPastEvents(event [, options] [, callback])
```
Gets past events for this contract.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| event | String | The name of the event in the contract, or `"allEvents"` to get all events. |
| options | Object | (optional) The options used for deployment.  See the table below for the details. |
| callback | Function | (optional) This callback will be fired with an array of event logs as the second argument, or an error as the first argument. |

To options object can contain the following:

| Name | Type | Description |
| --- | --- | --- |
| filter | Object | (optional) Lets you filter events by indexed parameters, *e.g.*, `{filter: {myNumber: [12,13]}}` means all events where "myNumber" is 12 or 13. |
| fromBlock | Number | (optional) The block number from which to get events on. |
| toBlock | Number | (optional) The block number to get events up to (defaults to `"latest"`). |
| topics | Array | (optional) This allows manually setting the topics for the event filter. If given the filter property and event signature, `topic[0]` will not be set automatically. |

**Return Value**

`Promise` returns `Array`: An array with the past event objects, matching the
given event name and filter.

**Example**

```javascript
> myContract.getPastEvents('MyEvent', {
      filter: {myIndexedParam: [20,23], myOtherIndexedParam: '0x123456789...'}, // Using an array means OR: e.g. 20 or 23
      fromBlock: 0,
      toBlock: 'latest'
  }, function(error, events) { console.log(events); })
  .then(function(events) {
      console.log(events) // same results as the optional callback above
  });

[{
    returnValues: {
        myIndexedParam: 20,
        myOtherIndexedParam: '0x123456789...',
        myNonIndexParam: 'My String'
    },
    raw: {
        data: '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385',
        topics: ['0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7', '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385']
    },
    event: 'MyEvent',
    signature: '0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7',
    logIndex: 0,
    transactionIndex: 0,
    transactionHash: '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385',
    blockHash: '0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7',
    blockNumber: 1234,
    address: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'
},{
      ...
}]
```


## caver.klay.net

The `caver-klay-net` package allows you to interact with the Klaytn nodes'
network properties.

```javascript
var Net = require('caver-klay-net');

// "Personal.providers.givenProvider" will be set if in a Klaytn supported browser.
var net = new Net(Net.givenProvider || 'ws://some.local-or-remote.node:8552');

// or using the caver package
var Caver = require('caver');
var caver = new Caver(Caver.givenProvider || 'ws://some.local-or-remote.node:8552');
// -> caver.klay.net
```


### getId

```javascript
caver.klay.net.getId([callback])
```

Gets the current network ID.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

`Promise` returns `Number` - The network ID.

**Example**

```javascript
> caver.klay.net.getId().then(console.log);
1000
```


### isListening

```javascript
caver.klay.net.isListening([callback])
```

Checks if the node is listening for peers.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

`Promise` returns `Boolean` - `true` if the node is listening for peers,
`false` otherwise.

**Example**

```javascript
> caver.klay.net.isListening().then(console.log);
true
```


### getPeerCount

```javascript
caver.klay.net.getPeerCount([callback])
```

Gets the number of peers connected to.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| callback | Function | (optional) Optional callback, returns an error object as the first parameter and the result as the second. |

**Return Value**

`Promise` returns `Number` - The number of peers connected to.

**Example**

```javascript
> caver.klay.net.getPeerCount().then(console.log);
10
```


## Truffle API Specification

At the moment, Klaytn supports Truffle v4.1.14 (core: 4.1.14).  Please find
details in [here](https://truffleframework.com/docs/truffle/overview).
We are planning to improve smart contract debugging and efficiency of
deployment functionality.  The specification will be updated when it is ready.
