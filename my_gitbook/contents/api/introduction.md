---
title: Introduction to API
sidebar: mydoc_sidebar_docs
permalink: api/introduction.html
folder: mydoc
---

## Enabling the Management APIs
To offer the APIs over the Klaytn RPC endpoints, please specify them with the `--${interface}api`
command line argument where `${interface}` can be `rpc` for the HTTP endpoint or `ws` for the WebSocket
endpoint.

`ipc` offers all APIs over the unix socket (Unix) or named pipe (Windows) endpoint without any flag.

You can launch a Klaytn node with specific APIs you want to add like the example below. But keep in mind that you can't change APIs once you launch the node.

Example 1) launching a Klaytn node with `klay` and `web3` modules enabled:
```shell
$ klay --rpcapi klay,web3 --rpc
```

Example 2) launching a Klaytn node with `klay`, `web3`, `miner`, and `personal` modules enabled:
```shell
$ klay --rpcapi klay,web3,miner,personal --rpc
```

The HTTP RPC interface must be explicitly enabled using the `--rpc` flag.

**NOTE**: Offering an API over the HTTP (`rpc`) or WebSocket (`ws`) interfaces will give everyone
access to the APIs who can access this interface (DApps, browser tabs, etc). Be careful which APIs
you enable. By default Klay enables all APIs over the IPC (`ipc`) interface and only the `klay`,
`net` and `web3` APIs over the HTTP and WebSocket interfaces.

To determine which APIs an interface provides, the `modules` JSON-RPC method can be invoked. For
example over an `rpc` interface:

**IPC**

```javascript
$ echo '{"jsonrpc":"2.0","method":"rpc_modules","params":[],"id":1}' | nc -U klay.ipc
```

**HTTP**

```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"rpc_modules","params":[],"id":1}' http://localhost:8551
```

will give all enabled modules including the version number:

```
{
   "jsonrpc":"2.0",
   "id":1,
   "result":{
      "admin":"1.0",
      "debug":"1.0",
      "klay":"1.0",
      "miner":"1.0",
      "net":"1.0",
      "personal":"1.0",
      "rpc":"1.0",
      "txpool":"1.0",
      "web3":"1.0"
   }
}
```

## Consuming the Management APIs

These additional APIs follow the same conventions as the official DApp APIs. Web3 can be
[extended](https://github.com/ethereum/web3.js/pull/229) and used to consume these additional APIs.

The different functions are split into multiple smaller logically grouped APIs. Examples are given
for the [JavaScript console](https://github.com/ethereum/go-ethereum/wiki/JavaScript-Console) but
can easily be converted to an RPC request.

### Example

**GetPrice**

* Console: `klay.gasPrice`

* IPC: `echo '{"jsonrpc":"2.0","method":"klay_gasPrice","params":[],"id":1}' | nc -U klay.ipc`

* HTTP: `curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_gasPrice","params":[],"id":1}' http://localhost:8551`

**Get Transaction Count**

With the address and lastest block as an arguments:

* Console: `klay.getTransactionCount("0xc94770007dda54cF92009BFF0dE90c06F603a09f")`

* IPC: `echo '{"jsonrpc":"2.0","method":"klay_getTransactionCount","params":["0xc94770007dda54cF92009BFF0dE90c06F603a09f","latest"],"id":1}' | nc -U klay.ipc`

* HTTP: `curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_getTransactionCount","params":["0xc94770007dda54cF92009BFF0dE90c06F603a09f","latest"],"id":1}' http://localhost:8551`

