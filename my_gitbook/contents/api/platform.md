---
title: Platform API
sidebar: mydoc_sidebar_docs
permalink: api/platform.html
folder: mydoc
---

## klay
### klay_protocolVersion

Returns the current Klaytn protocol version.

**Parameters**

None

**Return Value**

| Type   | Description                          |
| ------ | ------------------------------------ |
| String | The current Klaytn protocol version. |

**Example**

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_protocolVersion","params":[],"id":1}' http://localhost:8551

// Result
{
   "jsonrpc":"2.0",
   "id":1,
   "result":"0x40"
}
```


### klay_syncing

Returns an object with data about the sync status or `false`.

**Parameters**

None

**Return Value**

`Object|Boolean`, an object with sync status data or `false` when not syncing:

| Name          | Type     | Description                                                  |
| ------------- | -------- | ------------------------------------------------------------ |
| startingBlock | QUANTITY | The block at which the import started (will only be reset, after the sync reached his head). |
| currentBlock  | QUANTITY | The current block, same as klay_blockNumber.                 |
| highestBlock  | QUANTITY | The estimated highest block.                                 |

**Example**

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_syncing","params":[],"id":1}' http://localhost:8551

// Result
{
  "jsonrpc": "2.0",
  "id":1,
  "result": {
    startingBlock: '0x384',
    currentBlock: '0x386',
    highestBlock: '0x454'
  }
}
// Or when not syncing
{
  "jsonrpc": "2.0",
  "id":1,
  "result": false
}
```


### klay_coinbase

Returns the client coinbase address.

**Parameters**

None

**Return Value**

| Type | Description                             |
| ---- | --------------------------------------- |
| 20-byte DATA | The current coinbase address. |

**Example**

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_coinbase","params":[],"id":1}' http://localhost:8551

// Result
{
  "jsonrpc": "2.0",
  "id":1,
  "result": "0xc94770007dda54cF92009BFF0dE90c06F603a09f"
}
```


### klay_mining

Returns `true` if client is actively mining new blocks.

**Parameters**

None

**Return Value**

| Type    | Description                                        |
| ------- | -------------------------------------------------- |
| Boolean | `true` if the client is mining, otherwise `false`. |

**Example**

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_mining","params":[],"id":1}' http://localhost:8551

// Result
{
  "jsonrpc":"2.0",
  "id":1,
  "result":true
}
```

[//]: # "NOTE: removed klay_hashrate because it has no meaning on Klaytn. "

### klay_gasPrice

Returns the current price per gas in peb.

**NOTE**: This API has different behavior from Ethereum's and returns a gas price of Klaytn instead of suggesting a gas price as in Ethereum.

[//]: # " Developers can use `klay.gasPrice` to get `gas price` of Klaytn. "

**Parameters**

None

**Return Value**

| Type     | Description                              |
| -------- | ---------------------------------------- |
| QUANTITY | Integer of the current gas price in peb. |

**Example**

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_gasPrice","params":[],"id":1}' http://localhost:8551

// Result
{
  "jsonrpc": "2.0",
  "id":1,
  "result": "0x5d21dba00"
}
```


### klay_accounts

Returns a list of addresses owned by client.

**Parameters**

None

**Return Value**

| Type          | Description                              |
| ------------- | ---------------------------------------- |
| Array of 20-byte DATA | Addresses owned by the client. |

**Example**

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_accounts","params":[],"id":1}' http://localhost:8551

// Result
{
  "jsonrpc": "2.0",
  "id":1,
  "result": ["0xc94770007dda54cF92009BFF0dE90c06F603a09f"]
}
```


### klay_blockNumber

Returns the number of most recent block.

**Parameters**

None

**Return Value**

| Type     | Description                                           |
| -------- | ----------------------------------------------------- |
| QUANTITY | Integer of the current block number the client is on. |

**Example**

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_blockNumber","params":[],"id":83}' http://localhost:8551

// Result
{
  "jsonrpc": "2.0",
  "id":83,
  "result": "0xc94"
}
```


### klay_getBalance

Returns the balance of the account of given address.

**Parameters**

| Type           | Description                                                  |
| -------------- | ------------------------------------------------------------ |
| 20-byte DATA | Address to check for balance.                               |
| QUANTITY &#124; TAG | Integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter). |

**Return Value**

| Type     | Description                            |
| -------- | -------------------------------------- |
| QUANTITY | Integer of the current balance in peb. |

**Example**

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_getBalance","params":["0xc94770007dda54cF92009BFF0dE90c06F603a09f", "latest"],"id":1}' http://localhost:8551

// Result
{
  "jsonrpc": "2.0","id":1,
  "result": "0x0234c8a3397aab58" // 158972490234375000
}
```


### klay_getStorageAt

Returns the value from a storage position at a given address.

**Parameters**

| Type          | Description                                                  |
| ------------- | ------------------------------------------------------------ |
| 20-byte DATA | Address of the storage.                           |
| QUANTITY      | Integer of the position in the storage.                      |
| QUANTITY &#124; TAG | Integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter). |

 **Return Value**

| Type | Description                         |
| ---- | ----------------------------------- |
| DATA | The value at this storage position. |

**Example**

Calculating the correct position depends on the storage to retrieve. Consider the following contract deployed at `0x295a70b2de5e3953354a6a8344e616ed314d7251` by address `0x391694e7e0b0cce554cb130d723a9d27458f9298`.

```
contract Storage {
    uint pos0;
    mapping(address => uint) pos1;

    function Storage() {
        pos0 = 1234;
        pos1[msg.sender] = 5678;
    }
}
```

Retrieving the value of `pos0` is straight forward:

```javascript
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0", "method": "klay_getStorageAt", "params": ["0x295a70b2de5e3953354a6a8344e616ed314d7251", "0x0", "latest"], "id": 1}' http://localhost:8551

{"jsonrpc":"2.0","id":1,"result":"0x00000000000000000000000000000000000000000000000000000000000004d2"}
```

Retrieving an element of the map is harder. The position of an element in the map is calculated with:
```javascript
keccak(LeftPad32(key, 0), LeftPad32(map position, 0))
```

This means to retrieve the storage on `pos1["0x391694e7e0b0cce554cb130d723a9d27458f9298"]` we need to calculate the position with:
```javascript
keccak(decodeHex("000000000000000000000000391694e7e0b0cce554cb130d723a9d27458f9298" + "0000000000000000000000000000000000000000000000000000000000000001"))
```
The Klaytn console which comes with the `web3` library can be used to make the calculation
```javascript
> var key = "000000000000000000000000391694e7e0b0cce554cb130d723a9d27458f9298" + "0000000000000000000000000000000000000000000000000000000000000001"
undefined
> web3.sha3(key, {"encoding": "hex"})
"0x6661e9d6d8b923d5bbaab1b96e1dd51ff6ea2a93520fdc9eb75d059238b8c5e9"
```
Now to fetch the storage:
```javascript
curl -H "Content-Type: application/json" --data'{"jsonrpc":"2.0", "method": "klay_getStorageAt", "params": ["0x295a70b2de5e3953354a6a8344e616ed314d7251", "0x6661e9d6d8b923d5bbaab1b96e1dd51ff6ea2a93520fdc9eb75d059238b8c5e9", "latest"], "id": 1}' http://localhost:8551

{"jsonrpc":"2.0","id":1,"result":"0x000000000000000000000000000000000000000000000000000000000000162e"}
```


### klay_getTransactionCount

Returns the number of transactions *sent* from an address.

**Parameters**

| Type          | Description                                                  |
| ------------- | ------------------------------------------------------------ |
| 20-byte DATA | Address                                                      |
| QUANTITY &#124; TAG | Integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter). |

**Return Value**

| Type     | Description                                                  |
| -------- | ------------------------------------------------------------ |
| QUANTITY | Integer of the number of transactions send from this address. |

**Example**

 ```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_getTransactionCount","params":["0xc94770007dda54cF92009BFF0dE90c06F603a09f","latest"],"id":1}' http://localhost:8551

// Result
{
  "jsonrpc": "2.0",
  "id":1,
  "result": "0x1" // 1
}
 ```


### klay_getBlockTransactionCountByHash

Returns the number of transactions in a block from a block matching the given block hash.

**Parameters**

| Type | Description                |
| ---- | -------------------------- |
| 32-byte DATA | Hash of a block   |

**Return Value**

| Type     | Description                                          |
| -------- | ---------------------------------------------------- |
| QUANTITY | Integer of the number of transactions in this block. |

**Example**

```javascript
// Request
curl -H "Content-Type: application/json" --data'{"jsonrpc":"2.0","method":"klay_getBlockTransactionCountByHash","params":["0xc94770007dda54cF92009BFF0dE90c06F603a09f"],"id":1}' http://localhost:8551

// Result
{
  "jsonrpc": "2.0",
  "id":1,
  "result": "0xc" // 11
}
```


### klay_getBlockTransactionCountByNumber

Returns the number of transactions in a block matching the given block number.

**Parameters**

| Type          | Description                                                  |
| ------------- | ------------------------------------------------------------ |
| QUANTITY &#124; TAG | Integer of a block number, or the string `"earliest"`, `"latest"` or `"pending"`, as in the [default block parameter](#the-default-block-parameter). |

**Return Value**

| Type     | Description                                          |
| -------- | ---------------------------------------------------- |
| QUANTITY | Integer of the number of transactions in this block. |

**Example**

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_getBlockTransactionCountByNumber","params":["0xe8"],"id":1}' http://localhost:8551

// Result
{
  "jsonrpc": "2.0",
  "id":1,
  "result": "0xa" // 10
}
```


### klay_getCode

Returns code at a given address.

**Parameters**

| Type          | Description                                                  |
| ------------- | ------------------------------------------------------------ |
| 20-byte DATA | Address                                                      |
| QUANTITY &#124; TAG | Integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter). |

**Return Value**

| Type | Description                      |
| ---- | -------------------------------- |
| DATA | The code from the given address. |

**Example**

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_getCode","params":["0xa94f5374fce5edbc8e2a8697c15331677e6ebf0b", "0x2"],"id":1}' http://localhost:8551

// Result
{
  "jsonrpc": "2.0",
  "id":1,
  "result":   "0x600160008035811a818181146012578301005b601b6001356025565b8060005260206000f25b600060078202905091905056"
}
```


### klay_sign

The sign method calculates a Klaytn-specific signature with:
```
sign(keccak256("\x19Klaytn Signed Message:\n" + len(message) + message)))
```

Adding a prefix to the message makes the calculated signature recognizable as a Klaytn-specific signature. This prevents misuse where a malicious DApp can sign arbitrary data, *e.g.*, transaction, and use the signature to impersonate the victim.

**NOTE**: The address to sign with must be unlocked.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| account | 20-byte DATA | Address |
| message | N-byte DATA | Message to sign |

**Return Value**

| Type | Description |
| --- | --- |
| DATA | Signature |

**Example**

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_sign","params":["0x9b2055d370f73ec7d8a03e965129118dc8f5bf83", "0xdeadbeaf"],"id":1}' http://localhost:8551

// Result
{
  "jsonrpc": "2.0",
  "id":1,
  "result": "0xa3f20717a250c2b0b729b7e5becbff67fdaef7e0699da4de7ca5895b02a170a12d887fd3b17bfdce3481f10bea41f45ba9f709d39ce8325427b57afcfc994cee1b"
}
```

[//]: # "An example how to use solidity ecrecover to verify the signature calculated with `klay_sign` can be found [here](https://gist.github.com/bas-vk/d46d83da2b2b4721efb0907aecdb7ebd). The contract is deployed on the testnet Ropsten and Rinkeby."


### klay_sendTransaction

Creates a new message call transaction or a contract creation if the data field contains code.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| from | 20-byte DATA | The address from which the transaction is sent. |
| to | 20-byte DATA | (optional when creating a new contract) The address to which the transaction is directed. |
| gas | QUANTITY | (optional, default: 90000) Integer of the gas provided for the transaction execution. It will return unused gas. |
| gasPrice | QUANTITY | (optional, default: TBD) Integer of the gasPrice used for each paid gas. |
| value | QUANTITY | (optional) Integer of the value sent with this transaction. |
| data | DATA | The compiled code of a contract or the hash of the invoked method signature and encoded parameters. |
| nonce | QUANTITY | (optional) Integer of a nonce. This allows to overwrite your own pending transactions that use the same nonce. |

**Return Value**

| Type | Description |
| --- | --- |
| 32-byte DATA | The transaction hash, or the zero hash if the transaction is not yet available. |

Use [klay_getTransactionReceipt](#klay_gettransactionreceipt) to get the contract address, after the transaction was mined, when you created a contract.

**Example**

```javascript
params: [{
  "from": "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
  "to": "0xd46e8dd67c5d32be8058bb8eb970870f07244567",
  "gas": "0x76c0", // 30400
  "gasPrice": "0x9184e72a000", // 10000000000000
  "value": "0x9184e72a", // 2441406250
  "data": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"
}]
```

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_sendTransaction","params":[{see above}],"id":1}' http://localhost:8551

// Result
{
  "jsonrpc": "2.0","id":1,
  "result": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331"
}
```


### klay_sendRawTransaction

Creates a new message call transaction or a contract creation for signed transactions.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| None | DATA | The signed transaction data. |

**Return Value**

| Type | Description |
| --- | --- |
| 32-byte DATA | The transaction hash or the zero hash if the transaction is not yet available. |

Use [klay_getTransactionReceipt](#klay_gettransactionreceipt) to get the contract address, after the transaction was mined, when you created a contract.

**Example**

```javascript
params: ["0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"]
```

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_sendRawTransaction","params":[{see above}],"id":1}' http://localhost:8551

// Result
{
  "jsonrpc": "2.0",
  "id":1,
  "result": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331"
}
```


### klay_call

Executes a new message call immediately without creating a transaction on the block chain. It returns data or an error object of JSON RPC if error occurs.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| callObject | Object | The transaction call object.  See the next table for the object's properties. |
| blkNum  | QUANTITY &#124; TAG | Integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter). |

`callObject` has the following properties:

| Name | Type | Description |
| --- | --- | --- |
| from | 20-byte DATA | (optional) The address the transaction is sent from. |
| to | 20-byte DATA | The address the transaction is directed to. |
| gas | QUANTITY | (optional) Integer of the gas provided for the transaction execution. `klay_call` consumes zero gas, but this parameter may be needed by some executions. |
| gasPrice | QUANTITY | (optional) Integer of the gasPrice used for each paid gas. |
| value | QUANTITY | (optional) Integer of the value sent with this transaction. |
| data | DATA | (optional) Hash of the method signature and encoded parameters. |

[//]: # "For details see [Klaytn Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI)"

**Return Value**

| Type | Description |
| --- | --- |
| DATA | The return value of executed contract. |

Use [klay_getTransactionReceipt](#klay_gettransactionreceipt) to get the contract address, after the transaction was mined, when you created a contract.

**Error**

It returns an error object of JSON RPC if anything goes worng.
For example, an error object with message  "evm: execution reverted" will be generated if a message call is terminated with `REVERT` opcode.

**Example**

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc": "2.0", "method": "klay_call", "params": [{"from": "0x3f71029af4e252b25b9ab999f77182f0cd3bc085", "to": "0x87ac99835e67168d4f9a40580f8f5c33550ba88b", "gas": "0x100000", "gasPrice": "0x5d21dba00", "value": "0x0", "data": "0x8ada066e"}, "latest"], "id": 1}' http://localhost:8551

// Result
{"jsonrpc":"2.0","id":1,"result":"0x000000000000000000000000000000000000000000000000000000000000000a"}
```


### klay_estimateGas

Generates and returns an estimate of how much gas is necessary to allow the transaction to complete. The transaction will not be added to the blockchain. Note that the estimate may be significantly more than the amount of gas actually used by the transaction, for a variety of reasons including Klaytn Virtual Machine mechanics and node performance.

**Parameters**

See [klay_call](#klay_call) parameters, expect that all properties are optional. If no gas limit is specified, the Klaytn node uses the block gas limit from the pending block as an upper bound. As a result, the returned estimate might not be enough to executed the call/transaction when the amount of gas is higher than the pending block gas limit.

**Return Value**

| Type | Description |
| --- | --- |
| QUANTITY | The amount of gas used. |


**Example**
```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_estimateGas","params":[{see above}],"id":1}' http://localhost:8551

// Result
{
  "jsonrpc": "2.0","id":1,
  "result": "0x5208" // 21000
}
```


### klay_getBlockByHash

Returns information about a block by hash.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| None | 32-byte DATA | Hash of a block. |
| None | Boolean | If `true` it returns the full transaction objects, if `false` only the hashes of the transactions. |

**Return Value**

`Object` - A block object, or `null` when no block was found:

| Name | Type | Description |
| --- | --- | --- |
| number | QUANTITY | The block number. `null` when its pending block. |
| hash | 32-byte DATA | Hash of the block. `null` when its pending block. |
| parentHash | 32-byte DATA | Hash of the parent block. |
| nonce | 8-byte DATA | Hash of the generated proof-of-work. `null` when its pending block. |
| logsBloom | 256-byte DATA | The bloom filter for the logs of the block. `null` when its pending block. |
| transactionsRoot | 32-byte DATA | The root of the transaction trie of the block. |
| stateRoot | 32-byte DATA | The root of the final state trie of the block. |
| receiptsRoot | 32-byte DATA | The root of the receipts trie of the block. |
| miner | 20-byte DATA | The address of the beneficiary to whom the mining rewards were given. |
| difficulty | QUANTITY | Integer of the difficulty for this block. |
| totalDifficulty | QUANTITY | integer of the total difficulty of the chain until this block. |
| extraData | DATA | The "extra data" field of this block. |
| size | QUANTITY | Integer the size of this block in bytes. |
| gasLimit | QUANTITY | The maximum gas allowed in this block. |
| gasUsed | QUANTITY | The total used gas by all transactions in this block. |
| timestamp | QUANTITY | The Unix timestamp for when the block was collated. |
| transactions | Array | Array of transaction objects, or 32-byte transaction hashes depending on the last given parameter. |
| uncles | Array | Array of uncle hashes. |

**Example**

```javascript
params: [
   '0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331',
   true
]
```

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_getBlockByHash","params":["0x4e173f7f63d08eb78de755a5aa6a8b2362b48e4fee68e980b40b388017c1e275", true],"id":1}' http://localhost:8551

// Result
{
   "jsonrpc":"2.0",
   "id":1,
   "result":{
      "difficulty":"0x1",
      "extraData":"0xd6820302846b6c617986676f312e31318664617277696e000000000000000000f90164f85494207e38864b45a538733741dc1ff92eff9d1a6159946d64bc82b93368a7f963d6c34483ca3893f405f694bc9c19f91878369776812039e4ebcdfa3c64671694e3ed6fa287752b992f936b42360770c59731d9ebb841bffd17cbdf2c6c2e3f701b6edbf6eb94bce06bf11c5ea45ede53679e96c0679b034e83b1593552b3430a257389ef4f026c467a28c2e4499c50519c4837c21f3300f8c9b841f55bbcd5f79d9b8c6aa9831fa7a8dd5b1d6241456f5cbe6ea8c9c25185bab0c07dcc13fff9543713c1018b68e93267184c7c387c077002b2670738b636482cf501b841d297d35c19661ea42c092be94ef6729156fecfaf65cc2498cd5cf36aa6fd5ee63f7b2a7f75c4b2813c178591f84a40938e187f8b4129bdc6c7e5475b4b3905e300b841f91e9b54a1bae79a6b23287e8e781e3b9da35f51e1f1ca04a71f4ae3f0234d992a8c42f6f12d1419d73465efd64a44d1136ac6faf84dcf3892f81a637d7399f700",
      "gasLimit":"0xe8d4a50fff",
      "gasUsed":"0x0",
      "hash":"0x4e173f7f63d08eb78de755a5aa6a8b2362b48e4fee68e980b40b388017c1e275",
      "logsBloom":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
      "miner":"0x0000000000000000000000000000000000000000",
      "mixHash":"0x63746963616c2062797a616e74696e65206661756c7420746f6c6572616e6365",
      "nonce":"0x0000000000000000",
      "number":"0x1b4",
      "parentHash":"0xa619f41953d2cabf80d8da0ac65d70676a97d5fb1ec57846780dd2a82d5f466e",
      "receiptsRoot":"0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
      "sha3Uncles":"0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
      "size":"0x39e",
      "stateRoot":"0xb4c629becf900dc7f24b846b6c66849c6c5b63e0a776202575c96b82e73653c4",
      "timestamp":"0x5b9b77e3",
      "totalDifficulty":"0x1b5",
      "transactions":[

      ],
      "transactionsRoot":"0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421"
   }
}
```


### klay_getBlockByNumber

Returns information about a block by block number.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| None | QUANTITY &#124; TAG | Integer of a block number, or the string `"earliest"`, `"latest"` or `"pending"`, as in the [default block parameter](#the-default-block-parameter). |
| None | Boolean | If `true` it returns the full transaction objects, if `false` only the hashes of the transactions. |

**Return Value**

See [klay_getBlockByHash](#klay_getblockbyhash)

**Example**

```javascript
params: [
   '0x1b4', // 436
   true
]
```

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_getBlockByNumber","params":["0x1b4", true],"id":1}' http://localhost:8551
```


### klay_getTransactionByHash

Returns the information about a transaction requested by transaction hash.

**Parameters**

| Type | Description |
| --- | --- |
| 32-byte DATA | Hash of a transaction. |

**Return Value**

`Object` - A transaction object, or `null` when no transaction was found:

| Name | Type | Description |
| --- | --- | --- |
| blockHash | 32-byte DATA | Hash of the block where this transaction was in. `null` when its pending. |
| blockNumber | QUANTITY | Block number where this transaction was in. `null` when its pending. |
| from | 20-byte DATA | Address of the sender. |
| gas | QUANTITY | Gas provided by the sender. |
| gasPrice | QUANTITY | Gas price provided by the sender in peb. |
| hash | 32-byte DATA | Hash of the transaction. |
| input | DATA | The data send along with the transaction. |
| nonce | QUANTITY | The number of transactions made by the sender prior to this one. |
| to | 20-byte DATA | Address of the receiver. `null` when its a contract creation transaction. |
| transactionIndex | QUANTITY | Integer of the transactions index position in the block. `null` when its pending. |
| value | QUANTITY | Value transferred in peb. |
| v | QUANTITY | ECDSA recovery id |
| r | 32-byte DATA | ECDSA signature r |
| s | 32-byte DATA | ECDSA signature s |

**Example**

```javascript
params: [
   "0x88df016429689c079f3b2f6ad39fa052532c56795b733da78a91ebe6a713944b"
]
```

```javascript
// Request
curl -H "Content-Type: application/json"--data '{"jsonrpc":"2.0","method":"klay_getTransactionByHash","params":["0x45d75deee2a2107f9b913da3630b2da10152bad841823cad4dc5791e1f683c49"],"id":1}' http://localhost:8551

// Result
{
   "jsonrpc":"2.0",
   "id":1,
   "result":{
      "blockHash":"0x0dc898a1256eaf7adf01e0096c2866529aa45b64a31aefc93b0077443dcd4822",
      "blockNumber":"0x302f",
      "from":"0x5e30b4ac47cb421c2082715bdd605576f54e4878",
      "gas":"0x15f90",
      "gasPrice":"0x0",
      "hash":"0x45d75deee2a2107f9b913da3630b2da10152bad841823cad4dc5791e1f683c49",
      "input":"0x",
      "nonce":"0x123bf1",
      "to":"0x861465a1a8e09766a6446ba9249d4b615ca7f5a8",
      "transactionIndex":"0x1",
      "value":"0x1",
      "v":"0xfe8",
      "r":"0x170935b572e159596aa691ea5b180481f095aa4bbb48e74c5d4e2580521cd3e3",
      "s":"0x7b2aa76341d5e2103b6cfb1ee610dd3542733085cdb6bc7f087e90a1682ac914"
   }
}
```


### klay_getTransactionByBlockHashAndIndex

Returns information about a transaction by block hash and transaction index position.


**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| None | 32-byte DATA | Hash of a block. |
| None | QUANTITY | Integer of the transaction index position. |

**Return Value**

See [klay_getTransactionByHash](#klay_gettransactionbyhash)

**Example**

```javascript
params: [
   '0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331',
   '0x0' // 0
]
```

```javascript
// Request
curl -H "Content-Type: application/json" --data'{"jsonrpc":"2.0","method":"klay_getTransactionByBlockHashAndIndex","params":["0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b", "0x0"],"id":1}' http://localhost:8551
```


### klay_getTransactionByBlockNumberAndIndex

Returns information about a transaction by block number and transaction index position.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| None | QUANTITY &#124; TAG | A block number, or the string `"earliest"`, `"latest"` or `"pending"`, as in the [default block parameter](#the-default-block-parameter). |
| None | QUANTITY | The transaction index position. |

**Return Value**

See [klay_getTransactionByHash](#klay_gettransactionbyhash)

**Example**

```javascript
params: [
   '0x29c', // 668
   '0x0' // 0
]
```

```javascript
// Request
curl -H "Content-Type: application/json" --data'{"jsonrpc":"2.0","method":"klay_getTransactionByBlockNumberAndIndex","params":["0x29c", "0x0"],"id":1}' http://localhost:8551
```


### klay_getTransactionReceipt

Returns the receipt of a transaction by transaction hash.

**NOTE**: The receipt is not available for pending transactions.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| account | 32-byte DATA | Hash of a transaction. |

**Return Value**

`Object` - A transaction receipt object, or `null` when no receipt was found

| Name | Type | Description |
| --- | --- | --- |
| transactionHash | 32-byte DATA | Hash of the transaction. |
| transactionIndex | QUANTITY | Integer of the transactions index position in the block. |
| blockHash | 32-byte DATA | Hash of the block where this transaction was in. |
| blockNumber | QUANTITY | The block number where this transaction was in. |
| from | 20-byte DATA | Address of the sender. |
| to | 20-byte DATA | Address of the receiver. `null` when its a contract creation transaction. |
| cumulativeGasUsed | QUANTITY | The total amount of gas used when this transaction was executed in the block. |
| gasUsed | QUANTITY | The amount of gas used by this specific transaction alone. |
| contractAddress | DATA | The contract address created, if the transaction was a contract creation, otherwise `null`. |
| logs | Array | Array of log objects, which this transaction generated. |
| logsBloom | 256-byte DATA | Bloom filter for light clients to quickly retrieve related logs. |
| status | QUANTITY | Either `1` (success) or `0` (failure). |

**Example**

```javascript
params: [
   '0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238'
]
```

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_getTransactionReceipt","params":["0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238"],"id":1}' http://localhost:8551

// Result
{
   "jsonrpc":"2.0",
   "id":1,
   "result":{
      "blockHash":"0x0dc898a1256eaf7adf01e0096c2866529aa45b64a31aefc93b0077443dcd4822",
      "blockNumber":"0x302f",
      "contractAddress":null,
      "cumulativeGasUsed":"0xa410",
      "from":"0x5e30b4ac47cb421c2082715bdd605576f54e4878",
      "gasUsed":"0x5208",
      "logs":[],
  "logsBloom":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
      "status":"0x1",
      "to":"0x861465a1a8e09766a6446ba9249d4b615ca7f5a8",
      "transactionHash":"0x45d75deee2a2107f9b913da3630b2da10152bad841823cad4dc5791e1f683c49",
      "transactionIndex":"0x1"
   }
}
```


### klay_newFilter

Creates a filter object, based on filter options, to notify when the state changes (logs).
- To check if the state has changed, call [klay_getFilterChanges](#klay_getfilterchanges).
- To obtain all logs matching the filter created by `klay_newFilter`, call
  [klay_getFilterLogs](#klay_getfilterlogs).

**A note on specifying topic filters:**
Topics are order-dependent. A transaction with a log with topics `[A, B]` will be matched by the following topic filters:
* `[]` "anything"
* `[A]` "A in first position (and anything after)"
* `[null, B]` "anything in first position AND B in second position (and anything after)"
* `[A, B]` "A in first position AND B in second position (and anything after)"
* `[[A, B], [A, B]]` "(A OR B) in first position AND (A OR B) in second position (and anything after)"

**Parameters**

`Object` - The filter options:

| Name | Type | Description |
| --- | --- | --- |
| fromBlock | QUANTITY &#124; TAG | (optional, default: `"latest"`) Integer block number, or `"latest"` for the last mined block or `"pending"`, `"earliest"` for not yet mined transactions. |
| toBlock | QUANTITY &#124; TAG | (optional, default: `"latest"`) Integer block number, or `"latest"` for the last mined block or `"pending"`, `"earliest"` for not yet mined transactions. |
| address | 20-byte DATA &#124; Array | (optional) Contract address or a list of addresses from which logs should originate. |
| topics | Array of DATA | (optional) Array of 32-byte DATA topics. Topics are order-dependent. Each topic can also be an array of DATA with "or" options. |

**Return Value**

| Type | Description |
| --- | --- |
| QUANTITY | A filter id |

**Example**

```shell
// Request
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_newFilter","params":[{"fromBlock":"earliest","toBlock":"latest","address":"0x87ac99835e67168d4f9a40580f8f5c33550ba88b","topics":["0xd596fdad182d29130ce218f4c1590c4b5ede105bee36690727baa6592bd2bfc8"]}],"id":1}' http://localhost:8551

// Result
{"jsonrpc":"2.0","id":1,"result":"0xd32fd16b6906e67f6e2b65dcf48fc272"}
```


### klay_newBlockFilter

Creates a filter in the node, to notify when a new block arrives.
To check if the state has changed, call [klay_getFilterChanges](#klay_getfilterchanges).

**Parameters**

None

**Return Value**

| Type | Description |
| --- | --- |
| QUANTITY | A filter id. |

**Example**

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_newBlockFilter","params":[],"id":73}' http://localhost:8551

// Result
{
  "id":1,
  "jsonrpc":  "2.0",
  "result": "0x1" // 1
}
```


### klay_newPendingTransactionFilter

Creates a filter in the node, to notify when new pending transactions arrive.
To check if the state has changed, call [klay_getFilterChanges](#klay_getfilterchanges).

**Parameters**

None

**Return Value**

| Type | Description |
| --- | --- |
| QUANTITY | A filter id. |

**Example**

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_newPendingTransactionFilter","params":[],"id":73}' http://localhost:8551

// Result
{
  "id":1,
  "jsonrpc":  "2.0",
  "result": "0x1" // 1
}
```


### klay_uninstallFilter

Uninstalls a filter with given id. Should always be called when watch is no longer needed.
Additionally, filters timeout when they are not requested with [klay_getFilterChanges](#klay_getfilterchanges) for a period of time.

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| filter | QUANTITY | A filter id. |

**Return Value**

| Type | Description |
| --- | --- |
| Boolean | `true` if the filter was successfully uninstalled, otherwise `false`. |

**Example**

```javascript
params: [
  "0xb" // 11
]
```

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_uninstallFilter","params":["0xb"],"id":73}' http://localhost:8551

// Result
{
  "jsonrpc": "2.0","id":1,
  "result": true
}
```


### klay_getFilterChanges

Polling method for a filter, which returns an array of logs which occurred since last poll.

**Parameters**

| Name | Type | Description |
| --- | --- | ---|
| QUANTITY | string | The filter id (*e.g.*, "0x16" // 22). |

**Return Value**

`Array` - Array of log objects, or an empty array if nothing has changed since last poll.
- For filters created with [klay_newBlockFilter](#klay_newblockfilter), the return are block hashes (32-byte DATA),
  *e.g.*, `["0x3454645634534..."]`.
- For filters created with [klay_newPendingTransactionFilter](#klay_newpendingtransactionfilter), the return are transaction
  hashes (32-byte DATA), *e.g.*, `["0x6345343454645..."]`.
- For filters created with [klay_newFilter](#klay_newfilter), logs are objects with following parameters:

| Name | Type | Description |
| --- | --- | --- |
| removed | TAG | `true` when the log was removed, due to a chain reorganization. `false` if it is a valid log. |
| logIndex | QUANTITY | Integer of the log index position in the block. `null` when it is a pending log. |
| transactionIndex | QUANTITY | Integer of the transactions index position log was created from. `null` when pending. |
| transactionHash | 32-byte DATA | Hash of the transactions this log was created from. `null` when pending. |
| blockHash | 32-byte DATA | Hash of the block where this log was in. `null` when pending. |
| blockNumber | QUANTITY | The block number where this log was in. `null` when pending. |
| address | 20-byte DATA | Address from which this log originated. |
| data | DATA | Contains the non-indexed arguments of the log. |
| topics | Array of DATA | Array of 0 to 4 32-byte DATA of indexed log arguments. (In Solidity: The first topic is the hash of the signature of the event (*e.g.*, `Deposit(address,bytes32,uint256)`), except you declared the event with the `anonymous` specifier.). |

**Example**

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_getFilterChanges","params":["0x16"],"id":73}' http://localhost:8551

// Result
{
    "id":1,
    "jsonrpc":"2.0",
    "result": [{
    "logIndex": "0x1", // 1
    "blockNumber":"0x1b4", // 436
    "blockHash": "0x8216c5785ac562ff41e2dcfdf5785ac562ff41e2dcfdf829c5a142f1fccd7d",
    "transactionHash":  "0xdf829c5a142f1fccd7d8216c5785ac562ff41e2dcfdf5785ac562ff41e2dcf",
    "transactionIndex": "0x0", // 0
    "address": "0x16c5785ac562ff41e2dcfdf829c5a142f1fccd7d",
    "data":"0x0000000000000000000000000000000000000000000000000000000000000000",
    "topics": ["0x59ebeb90bc63057b6515673c3ecf9438e5058bca0f92585014eced636878c9a5"]
    },{
        ...
    }]
}
```


### klay_getFilterLogs

Returns an array of all logs matching filter with given id, which has been
obtained using [klay_newFilter](#klay_newfilter).  Note that filter ids
returned by other filter creation functions, such as [klay_newBlockFilter](#klay_newblockfilter)
or [klay_newPendingTransactionFilter](#klay_newpendingtransactionfilter),
cannot be used with this function.

**Parameters**

| Name | Type | Description |
| --- | --- | ---|
| QUANTITY | string | The filter id |

**Return Value**

See [klay_getFilterChanges](#klay_getfilterchanges)

**Example**

```shell
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_getFilterLogs","params":["0xd32fd16b6906e67f6e2b65dcf48fc272"],"id":1}' http://localhost:8551

// Result
{"jsonrpc":"2.0","id":1,"result":[{"address":"0x87ac99835e67168d4f9a40580f8f5c33550ba88b","topics":["0xd596fdad182d29130ce218f4c1590c4b5ede105bee36690727baa6592bd2bfc8"],"data":"0x0000000000000000000000000000000000000000000000000000000000000064000000000000000000000000000000000000000000000000000000000000007b","blockNumber":"0x54","transactionHash":"0xcd4703cd62bd930d4652999bce8dcb75b7ade49d922fa42dc11e568c52a5fa6f","transactionIndex":"0x0","blockHash":"0x9a49f30f1d1876ff3913bd0aa58f328822e7a369cb13e0640b82234f26e781bb","logIndex":"0x0","removed":false}]}
```


### klay_getLogs

Returns an array of all logs matching a given filter object.

**Parameters**

`Object` - The filter options:

| Name | Type | Description |
| --- | --- | ---|
| fromBlock | QUANTITY &#124; TAG | (optional, default: `"latest"`) Integer block number, or `"latest"` for the last mined block or `"pending"`, `"earliest"` for not yet mined transactions. |
| toBlock | QUANTITY &#124; TAG | (optional, default: `"latest"`) Integer block number, or `"latest"` for the last mined block or `"pending"`, `"earliest"` for not yet mined transactions. |
| address | 20-byte DATA &#124; Array | (optional) Contract address or a list of addresses from which logs should originate. |
| topics | Array of DATA | (optional) Array of 32-byte DATA topics. Topics are order-dependent. Each topic can also be an array of DATA with “or” options. |
| blockHash | 32-byte DATA | (optional) A filter option that restricts the logs returned to the single block with the 32-byte hash blockHash. Using blockHash is equivalent to fromBlock = toBlock = the block number with hash blockHash. If blockHash is present in in the filter criteria, then neither fromBlock nor toBlock are allowed. |

**Return Value**

See [klay_getFilterChanges](#klay_getfilterchanges)

**Examples**

```shell
// Request
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_getLogs","params":[{"fromBlock":"0x1","toBlock":"latest","address":"0x87ac99835e67168d4f9a40580f8f5c33550ba88b"}],"id":1}' http://localhost:8551

// Result
{"jsonrpc":"2.0","id":1,"result":[{"address":"0x87ac99835e67168d4f9a40580f8f5c33550ba88b","topics":["0xd596fdad182d29130ce218f4c1590c4b5ede105bee36690727baa6592bd2bfc8"],"data":"0x0000000000000000000000000000000000000000000000000000000000000064000000000000000000000000000000000000000000000000000000000000007b","blockNumber":"0x5e4","transactionHash":"0xb4ff28cbe51326de2e15a582980c0285ea0d5c1478a6bb70ec34b820cabd9841","transactionIndex":"0x0","blockHash":"0x88649869ea81a70c90b4ce01d3f9e6ba49f4d42887ec2e615cd4d0b777026835","logIndex":"0x0","removed":false}]}
```

```shell
// Request
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_getLogs","params":[{"fromBlock":"earliest","toBlock":"latest","topics":["0xd596fdad182d29130ce218f4c1590c4b5ede105bee36690727baa6592bd2bfc8"]}],"id":2}' http://localhost:8551

// Result
{"jsonrpc":"2.0","id":2,"result":[{"address":"0x87ac99835e67168d4f9a40580f8f5c33550ba88b","topics":["0xd596fdad182d29130ce218f4c1590c4b5ede105bee36690727baa6592bd2bfc8"],"data":"0x0000000000000000000000000000000000000000000000000000000000000064000000000000000000000000000000000000000000000000000000000000007b","blockNumber":"0x5e4","transactionHash":"0xb4ff28cbe51326de2e15a582980c0285ea0d5c1478a6bb70ec34b820cabd9841","transactionIndex":"0x0","blockHash":"0x88649869ea81a70c90b4ce01d3f9e6ba49f4d42887ec2e615cd4d0b777026835","logIndex":"0x0","removed":false},{"address":"0x87ac99835e67168d4f9a40580f8f5c33550ba88b","topics":["0xd596fdad182d29130ce218f4c1590c4b5ede105bee36690727baa6592bd2bfc8"],"data":"0x0000000000000000000000000000000000000000000000000000000000000064000000000000000000000000000000000000000000000000000000000000007b","blockNumber":"0x7dc","transactionHash":"0x939d144e255179e6121908fc0fcc1a3df8d0a54ce174e5c04cf439c53d58dac7","transactionIndex":"0x0","blockHash":"0xea90a0d77ba9f3eab234aaa5c097d11019ea2ff103062592e067d22efb545c76","logIndex":"0x0","removed":false}]}
```


### klay_getWork

Returns the hash of the current block, the seedHash, and the boundary condition to be met ("target").

**Parameters**

None

**Return Value**

| Type | Description |
| --- | --- |
| Array | Array with the following properties: <br> 1. 32-byte DATA - current block header pow-hash <br> 2. 32-byte DATA - the seed hash used for the DAG. <br> 3. 32-byte DATA - the boundary condition ("target"), 2<sup>256</sup> / difficulty. |

**Example**

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_getWork","params":[],"id":73}' http://localhost:8551

// Result
{
    "id":1,
    "jsonrpc":"2.0",
    "result": [
        "0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef",
        "0x5EED00000000000000000000000000005EED0000000000000000000000000000",
        "0xd1ff1c01710000000000000000000000d1ff1c01710000000000000000000000"
    ]
}
```


[//]: # "NOTE: removed klay_submitWork because it is irrelevant to Klaytn. "
[//]: # "NOTE: removed klay_submitHashrate because it is irrelevant to Klaytn. "
[//]: # "TODO: delete "## db ## shh""

### klay_getValidators
Returns a list of all validators at the specified block. If the parameter is not set, returns a list of all validators at the latest block.

**Parameters**

| Name | Type | Description |
| --- | --- | ---|
| QUANTITY  &#124; TAG | Integer | (optional) Integer of a block number, or the string `"earliest"` or `"latest"`. |

**Return Value**

`Object` - A block object, or `null` when no block was found:

| Type | Description |
| --- | ---|
| Array of 20-byte DATA | Addresses of all validators. |

**Example**

```javascript
params: [
    '0x1b4', // 436
]
```

```javascript
// Request
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0", "method":"klay_getValidators", "params":["0x1b4"],"id":73}' http://localhost:8551
// Result
{
    "jsonrpc":"2.0",
    "id":73,
    "result":[
        "0x207e38864b45a538733741dc1ff92eff9d1a6159",
        "0x6d64bc82b93368a7f963d6c34483ca3893f405f6",
        "0xbc9c19f91878369776812039e4ebcdfa3c646716",
        "0xe3ed6fa287752b992f936b42360770c59731d9eb"
    ]
}
```

### klay_getBlockWithConsensusInfoByHash

Returns a block with consensus information matched by the given hash.

**Parameters**

| Type | Description |
| --- | ---|
| 32-byte DATA | Hash of a block. |

**Return Value**

`Object` - A block object with consensus information (a proposer and a list of committee members), or `null` when no block was found:

| Name | Type | Description |
| --- | --- | ---|
| committee | Array | Array of addresses of committee members of this block. The committee is a subset of validators participated in the consensus protocol for this block. |
| gasLimit | QUANTITY | The maximum gas allowed in this block. |
| gasUsed  | QUANTITY | The total used gas by all transactions in this block. |
| hash     | 32-byte DATA | Hash of the block. `null` when its pending block. |
| miner    | 20-byte DATA | TBD |
| nonce    | 8-byte DATA | TBD |
| number   | QUANTITY | The block number. `null` when its pending block. |
| parentHash | 32-byte DATA | Hash of the parent block. |
| proposer | 20-byte DATA | The address of the block proposer. |
| receiptsRoot | 32-byte DATA | The root of the receipts trie of the block. |
| size | QUANTITY | Integer the size of this block in bytes. |
| stateRoot | 32-byte DATA | The root of the final state trie of the block. |
| timestamp | QUANTITY | The Unix timestamp for when the block was collated. |
| transactions | Array | Array of transaction objects. |
| transactionsRoot | 32-byte DATA | The root of the transaction trie of the block. |

**Example**

```javascript
params: [
    '0x2f0c986bd224d85411c89c108c28caa6dad1c7f97745051920289c65924faf10',
]
```

```javascript
// Request
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0", "method":"klay_getBlockWithConsensusInfoByHash", "params":["0x2f0c986bd224d85411c89c108c28caa6dad1c7f97745051920289c65924faf10"],"id":73}' http://localhost:8551

// Result
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

### klay_getBlockWithConsensusInfoByNumber
Returns a block with consensus information matched by the given block number.

**Parameters**

| Type | Description |
| --- | ---|
| QUANTITY &#124; TAG | Integer of a block number, or the string `"earliest"` or `"latest"`. |

```javascript
params: [
    '0x1'
]
```

**Return Value**

`Object` - A block object with consensus information (a proposer and a list of committee members), or `null` when no block was found:

| Name | Type | Description |
| --- | --- | ---|
| committee | Array | Array of addresses of committee members of this block. The committee is a subset of validators participated in the consensus protocol for this block. |
| gasLimit | QUANTITY | The maximum gas allowed in this block. |
| gasUsed | QUANTITY | The total used gas by all transactions in this block. |
| hash | 32-byte DATA | Hash of the block. `null` when its pending block. |
| miner | 20-byte DATA | TBD |
| nonce | 8-byte DATA | TBD |
| number | QUANTITY | The block number. `null` when its pending block. |
| parentHash | 32-byte DATA | Hash of the parent block. |
| proposer | 20-byte DATA | The address of the block proposer. |
| receiptsRoot | 32-byte DATA | The root of the receipts trie of the block. |
| size | QUANTITY | Integer the size of this block in bytes. |
| stateRoot | 32-byte DATA | The root of the final state trie of the block. |
| timestamp | QUANTITY | The Unix timestamp for when the block was collated. |
| transactions | Array | Array of transaction objects. |
| transactionsRoot | 32-byte DATA | The root of the transaction trie of the block. |

**Example**

```javascript
// Request
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0", "method":"klay_getBlockWithConsensusInfoByNumber", "params":["0x1"],"id":73}'http://localhost:8551

// Result
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


## web3

### web3_clientVersion

Returns the current client version.

**Parameters**

None

**Return Value**

| Type | Description |
| --- | ---|
| String | The current client version. |

**Example**

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"web3_clientVersion","params":[],"id":67}' http://localhost:8551

// Result
{
    "jsonrpc":"2.0",
    "id":67,
    "result":"Klaytn/validator-0/vv0.3.2+27df0a14b8/darwin-amd64/go1.11"
}
```


### web3_sha3

Returns Keccak-256 (*not* the standardized SHA3-256) of the given data.

**Parameters**

| Type | Description |
| --- | --- |
| DATA | The data to convert into a SHA3 hash. |

**Return Value**

| Type | Description |
| --- | --- |
| DATA | The SHA3 result of the given string. |

**Example**

```javascript
params: [
    "0x68656c6c6f20776f726c64"
]
```

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"web3_sha3","params":["0x68656c6c6f20776f726c64"],"id":64}' http://localhost:8551

// Result
{
    "jsonrpc":"2.0",
    "id":64,
    "result":"0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
}
```


## net
### net_version

Returns the current network id.

**Parameters**

None

**Return Value**

| Type | Description |
| --- | --- |
| String | The current network id.<br>    - `"1000"`: Klaytn Aspen testnet. |

**Example**

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"net_version","params":[],"id":67}' http://localhost:8551

// Result
{
    "jsonrpc":"2.0",
    "id":67,
    "result":"2018"
}
```


### net_listening

Returns `true` if the client is actively listening for network connections.

**Parameters**

None

**Return Value**

| Type | Description |
| --- | --- |
| Boolean | `true` when listening, otherwise `false`. |

**Example**

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"net_listening","params":[],"id":67}' http://localhost:8551

// Result
{
    "id":67,
    "jsonrpc":"2.0",
    "result":true
}
```


### net_peerCount

Returns the number of peers currently connected to the client.

**Parameters**

None

**Return Value**

| Type | Description |
| --- | --- |
| QUANTITY | Integer of the number of connected peers.|

**Example**

```javascript
// Request
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":74}' http://localhost:8551

// Result
{
    "id":74,
    "jsonrpc": "2.0",
    "result": "0x3" // 2
}
```


## The Default Block Parameter

The following methods have an extra default block parameter:

- [klay_getBalance](#klay_getbalance)
- [klay_getCode](#klay_getcode)
- [klay_getTransactionCount](#klay_gettransactioncount)
- [klay_getStorageAt](#klay_getstorageat)
- [klay_call](#klay_call)

When requests are made that act on the state of Klaytn, the last default block parameter determines the height of the block.

The following options are possible for the defaultBlock parameter:

- `HEX String` - an integer block number
- `String "earliest"` for the earliest/genesis block
- `String "latest"` - for the latest mined block
- `String "pending"` - for the pending state/transactions
