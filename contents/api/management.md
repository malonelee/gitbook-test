---
title: Management APIs
sidebar: mydoc_sidebar_docs
permalink: api/management.html
folder: mydoc
---

Beside the officially exposed DApp API namespaces (`klay`, `web3`), Klaytn provides the following
extra management API namespaces:

* `admin`: Klaytn node management
* `debug`: Klaytn node debugging
* `personal`: Account management
* `txpool`: Transaction pool inspection

[//]: # "* `miner`: Miner management"
[//]: # "* `istalbul` : Istanbul BFT management // more detailed contents will be updated soon"

[//]: # "* `miner`: Miner and [DAG](https://github.com/ethereum/wiki/wiki/Ethash-DAG) management"


## Admin

The `admin` API gives you access to several non-standard RPC methods, which will allow you to have
a fine grained control over your Klaytn instance, including but not limited to network peer and RPC
endpoint management.

### admin_addPeer

The `addPeer` administrative method requests adding a new remote node to the list of tracked static
nodes. The node will try to maintain connectivity to these nodes at all times, reconnecting every
once in a while if the remote connection goes down.

The method accepts a single argument, the [`enode`](https://github.com/ethereum/wiki/wiki/enode-url-format)
URL of the remote peer to start tracking and returns a `BOOL` indicating whether the peer was accepted
for tracking or some error occurred.

| Client  | Method invocation                              |
|:-------:|------------------------------------------------|
| Console | `admin.addPeer(url)`                           |
| RPC     | `{"method": "admin_addPeer", "params": [url]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| url | string | Peer's `enode` URL. |

**Return Value**

| Type | Description |
| --- | --- |
| bool | `true` if the peer was accepted, `false` otherwise. |

**Example**

Console
```javascript
> admin.addPeer("enode://a979fb575495b8d6db44f750317d0f4622bf4c2aa3365d6af7c284339968eef29b69ad0dce72a4d8db5ebb4968de0e3bec910127f134779fbcb0cb6d3331163c@10.0.0.1:32323") //This is an example IP address.
true
```
HTTP RPC

```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"admin_addPeer","params":["enode://a979fb575495b8d6db44f750317d0f4622bf4c2aa3365d6af7c284339968eef29b69ad0dce72a4d8db5ebb4968de0e3bec910127f134779fbcb0cb6d3331163c@10.0.0.1:32323"],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":true}
```


### admin_datadir

The `datadir` administrative property can be queried for the absolute path the running Klaytn node
currently uses to store all its databases.

| Client  | Method invocation                 |
|:-------:|-----------------------------------|
| Console | `admin.datadir`                   |
| RPC     | `{"method": "admin_datadir"}`     |

**Parameters**

None

**Return Value**

| Type | Description |
| --- | --- |
| string | The `datadir` path. |

**Example**

Console

```javascript
> admin.datadir
"/home/user/Library/Klaytn"
```
HTTP RPC

```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"admin_datadir","id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":"/your/dir/klaytn/data/dd"}
```

### admin_nodeInfo

The `nodeInfo` administrative property can be queried for all the information known about the running
Klaytn node at the networking granularity. These include general information about the node itself as a
participant of the [devp2p](https://github.com/ethereum/devp2p/blob/master/devp2p.md) P2P
overlay protocol, as well as specialized information added by each of the running application protocols,
e.g., `klay`.

| Client  | Method invocation                         |
|:-------:|-------------------------------------------|
| Console | `admin.nodeInfo`                          |
| RPC     | `{"method": "admin_nodeInfo"}`            |

**Parameters**

None

**Return Value**

| Type | Description |
| --- | --- |
| JSON string | The node information. |

**Example**

Console
```javascript
> admin.nodeInfo
{
   enode: "enode://0bbff960d26fc12a5153ac25d7aaffd654e073a74a8b1aa65034250d47fac610ebe99a83d21d741c6121a32fb01312b49fc0633ae04e80c5eb73c3bc71c5a850@[::]:32323?discport=0",
   id: "0bbff960d26fc12a5153ac25d7aaffd654e073a74a8b1aa65034250d47fac610ebe99a83d21d741c6121a32fb01312b49fc0633ae04e80c5eb73c3bc71c5a850",
   ip: "::",
   listenAddr: "[::]:32323",
   name: "Klaytn/validator-1/vv0.3.2+08f6479b53/darwin-amd64/go1.10.3",
   ports: {
     discovery: 0,
     listener: 32323
   },
   protocols: {
     istanbul: {
       config: {
         chainId: 2018,
         isBFT: true,
         istanbul: {...},
         unitPrice: 0
       },
       difficulty: 52794,
       genesis: "0x42824367c973785245923a712cf2e5a99aae6a26f44e4f1ec686a0e60986644e",
       head: "0x4c3000a6f8c40b0507d8ee4a3fc5c9865df0a8d66f882366ea95473c87342005",
       network: 2017
     }
   }
}
```

HTTP RPC

```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"admin_nodeInfo","id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,
"result":               {"id":"377ef808aff73a397d133b3bf160df586054c98c0e6a65c8fce9560e6a0632bc975419f461803d27f28ee270287113cc2359225814debc1bfb2f811061e14c5d", "name":"Klaytn/vv0.4.1/darwin-amd64/go1.11",    "enode":"enode://377ef808aff73a397d133b3bf160df586054c98c0e6a65c8fce9560e6a0632bc975419f461803d27f28ee270287113cc2359225814debc1bfb2f811061e14c5d@[::]:32323?discport=0",
"ip":"::",
"ports":{"discovery":0,"listener":32323},
"listenAddr":"[::]:32323",
"protocols":{"istanbul":{"network":1000,"difficulty":1,"genesis":"0x06806bd8b1e086dfb7098a289da07037a3af58e793d205d20f61c88eeea9351d","config":{"chainId":1000,"istanbul":{"epoch":30000,"policy":0,"sub":7},"isBFT":true,"unitPrice":25000000000,"deriveShaImpl":0},"head":"0x06806bd8b1e086dfb7098a289da07037a3af58e793d205d20f61c88eeea9351d"}}}}
```




### admin_peers

The `peers` administrative property can be queried for all the information known about the connected
remote nodes at the networking granularity. These include general information about the nodes themselves
as participants of the [devp2p](https://github.com/ethereum/devp2p/blob/master/devp2p.md)
P2P overlay protocol, as well as specialized information added by each of the running application
protocols.

| Client  | Method invocation           |
| :-----: | --------------------------- |
| Console | `admin.peers`               |
|   RPC   | `{"method": "admin_peers"}` |

**Parameters**

None

**Return Value**

| Type | Description |
| --- | --- |
| JSON string | The information about all connected peers. |

**Example**

Console

```javascript
> admin.peers
[{
    caps: ["istanbul/64"],
    id: "5d73afadf1eb4d6ccd1e10ab0f00301a1642b102fb521f170f4eaa4b3cb9a58788d1e2b387d6ce3726cb4786d034feb7dd17b5055b6d9a888520011e5756c89e",
    name: "Klaytn/validator-3/vv0.3.2+08f6479b53/darwin-amd64/go1.10.3",
    network: {
      inbound: true,
      localAddress: "127.0.0.1:32323",
      remoteAddress: "127.0.0.1:63323",
      static: false,
      trusted: false
    },
    protocols: {
      istanbul: {
        difficulty: 52794,
        head: "0x4c3000a6f8c40b0507d8ee4a3fc5c9865df0a8d66f882366ea95473c87342005",
        version: 64
      }
    }
},  /* ... */ {
    caps: ["istanbul/64"],
    id: "8bcf4297aa6bb46121bb20a18b7af8f1eaad7e7435c71cb64109511a73c5507744bca138ee76b52d06cecedde9d88fdfddbffc5c3b80c5cbace3c326d5df5f1f",
    name: "Klaytn/validator-2/vv0.3.2+08f6479b53/darwin-amd64/go1.10.3",
    network: {
      inbound: true,
      localAddress: "127.0.0.1:32323",
      remoteAddress: "127.0.0.1:63247",
      static: false,
      trusted: false
    },
    protocols: {
      istanbul: {
        difficulty: 52794,
        head: "0x4c3000a6f8c40b0507d8ee4a3fc5c9865df0a8d66f882366ea95473c87342005",
        version: 64
      }
    }
}]
```
HTTP RPC

**NOTE**: All IP addresses below are shown as examples. Please replace them with the actual IP addresses in your execution environment.

```shell
curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"admin_peers","id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":[{"id":"144af69d2bb030c6a2a5ceee7445dc613e200f19358547cffc353d56e6c8a5b4186a6953c028b6afd0ab3c2bfc4c86f24b0bf855d0686b964ec65cefd3deec37","name":"Klaytn/vv0.4.1/linux-amd64/go1.9.4","caps":["istanbul/64"],"network":{"localAddress":"10.0.10.1:49355","remoteAddress":"10.0.0.1:32323","inbound":false,"trusted":false,"static":true},"protocols":{"istanbul":{"version":64,"difficulty":1285901,"head":"0x2d04ac52df4af08a9a0e15d5939c29decb00031e7b3f6abd05bc0c731f6b5561"}}},{"id":"a875620f67f0b12edb97d0ec269e7940f2505b1f62576f39858c37e1d7f956318c3a619239f03f806a79ccaa8e7e9b5def343c24a9fd2e9d715964e0952dd995","name":"Klaytn/vv0.4.1/linux-amd64/go1.9.4","caps":["istanbul/64"],"network":{"localAddress":"10.0.10.2:49353","remoteAddress":"10.0.0.2:32323","inbound":false,"trusted":false,"static":true},"protocols":{"istanbul":{"version":64,"difficulty":1285901,"head":"0x2d04ac52df4af08a9a0e15d5939c29decb00031e7b3f6abd05bc0c731f6b5561"}}},{"id":"e18d6d4e0ffac0a51028a8d49a548295ac8ac50d064f3581600799a3ae761a61f0b39c38b4195e163e01f30db616debf61b5b2ddea716bc8fb1c907ce7a1de26","name":"Klaytn/vv0.4.1/linux-amd64/go1.9.4","caps":["istanbul/64"],"network":{"localAddress":"10.0.10.3:49354","remoteAddress":"10.0.0.3:32323","inbound":false,"trusted":false,"static":true},"protocols":{"istanbul":{"version":64,"difficulty":1285900,"head":"0x2e228a45c7c9b9e6729b6c66b31957d6cb62ce53e32cedf156615a4e8a2e253a"}}}]}
```


### admin_startRPC

The `startRPC` administrative method starts an HTTP based [JSON RPC](http://www.jsonrpc.org/specification)
API webserver to handle client requests.

The method returns a boolean flag specifying whether the HTTP RPC listener was opened or not. Please note, only one HTTP endpoint is allowed to be active at any time.

| Client  | Method invocation                                            |
| :-----: | ------------------------------------------------------------ |
| Console | `admin.startRPC(host, port, cors, apis)`                     |
|   RPC   | `{"method": "admin_startRPC", "params": [host, port, cors, apis]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| host | string | (optional) network interface to open the listener socket on (defaults to `"localhost"`). |
| port | int | (optional) network port to open the listener socket on (defaults to `8551`). |
| cors | string | (optional) [cross-origin resource sharing](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) header to use (defaults to `""`). |
| apis | string | (optional) API modules to offer over this interface (defaults to `"klay,net,web3"`). |

**Return Value**

| Type | Description |
| --- | --- |
| bool | `true` if the HTTP RPC listener was opened, `false` if not. |

**Example**

```javascript
> admin.startRPC("127.0.0.1", 8551)
true
```


### admin_startWS

The `startWS` administrative method starts an WebSocket based [JSON RPC](http://www.jsonrpc.org/specification)
API webserver to handle client requests.

The method returns a boolean flag specifying whether the WebSocket RPC listener was opened or not. Please note, only one WebSocket endpoint is allowed to be active at any time.

| Client  | Method invocation                                            |
| :-----: | ------------------------------------------------------------ |
| Console | `admin.startWS(host, port, cors, apis)`                      |
|   RPC   | `{"method": "admin_startWS", "params": [host, port, cors, apis]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| host | string | (optional) network interface to open the listener socket on (defaults to `"localhost"`). |
| port | int | (optional) network port to open the listener socket on (defaults to `8552`). |
| cors | string | (optional) [cross-origin resource sharing](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) header to use (defaults to `""`). |
| apis | string | (optional) API modules to offer over this interface (defaults to `"klay,net,web3"`). |

**Return Value**

| Type | Description |
| --- | --- |
| bool | `true` if the WebSocket RPC listener was opened, `false` if not. |

**Example**

```javascript
> admin.startWS("127.0.0.1", 8552)
true
```


### admin_stopRPC

The `stopRPC` administrative method closes the currently open HTTP RPC endpoint. As the node can only have a single HTTP endpoint running, this method takes no parameters, returning a boolean whether the endpoint was closed or not.

| Client  | Method invocation             |
| :-----: | ----------------------------- |
| Console | `admin.stopRPC()`             |
|   RPC   | `{"method": "admin_stopRPC"}` |

**Parameters**

None

**Return Value**

| Type | Description |
| --- | --- |
| bool | `true` if the endpoint was closed, `false` if not. |

**Example**

Console

```javascript
> admin.stopRPC()
true
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"admin_stopRPC","id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":true}
```


### admin_stopWS

The `stopWS` administrative method closes the currently open WebSocket RPC endpoint. As the node can only have a single WebSocket endpoint running, this method takes no parameters, returning a boolean whether the endpoint was closed or not.

| Client  | Method invocation            |
| :-----: | ---------------------------- |
| Console | `admin.stopWS()`             |
|   RPC   | `{"method": "admin_stopWS"}` |

**Parameters**

None

**Return Value**

| Type | Description |
| --- | --- |
| bool | `true` if the endpoint was closed, `false` if not. |

**Example**

Console

```javascript
> admin.stopWS()
true
```



## Debug

The `debug` API gives you access to several non-standard RPC methods, which will allow you to inspect,
debug and set certain debugging flags at run time.


### debug_startPProf

Starts the pprof HTTP server.  The running pprof server can be accessed by
(when the default configuration, i.e., localhost:6060, is used):
- http://localhost:6060/debug/pprof (for the pprof results)
- http://localhost:6060/memsize/ (for the memory size reports)
- http://localhost:6060/debug/vars (for the metrics)

| Client  | Method invocation                                            |
| :-----: | ------------------------------------------------------------ |
| Console | `debug.startPProf(address, port)`                            |
| RPC     | `{"method": "debug_startPProf", "params": [string, number]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| address | string | (optional) pprof HTTP server listening interface (default: "127.0.0.1"). |
| port | int | (optional) pprof HTTP server listening port (default: 6060). |

**Return Value**

None

**Example**

Console
```javascript
# To start the pprof server at 127.0.0.1:6060
> debug.startPProf()
null

# To start the pprof server at localhost:12345
> debug.startPProf("localhost", 12345)
null
```

HTTP RPC
```shell
# To start the pprof server at localhost:6060
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_startPProf","params":["localhost", 6060],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":null}
```


### debug_stopPProf

Stops the pprof HTTP server.

| Client  | Method invocation                             |
| :-----: | --------------------------------------------- |
| Console | `debug.stopPProf()`                           |
| RPC     | `{"method": "debug_stopPProf", "params": []}` |

**Parameters**

None

**Return Value**

None

**Example**

Console
```javascript
> debug.stopPProf()
null
```

HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_stopPProf","params":[],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":null}
```


### debug_isPProfRunning

Returns `true` if the pprof HTTP server is running and `false` otherwise.

| Client  | Method invocation                                  |
| :-----: | -------------------------------------------------- |
| Console | `debug.isPProfRunning()`                           |
| RPC     | `{"method": "debug_isPProfRunning", "params": []}` |

**Parameters**

None

**Return Value**

| Type | Description |
| --- | --- |
| bool | `true` if the pprof HTTP server is running and `false` otherwise. |

**Example**

Console
```javascript
> debug.isPProfRunning()
false
```

HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_isPProfRunning","params":[],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":true}
```


### debug_backtraceAt

Sets the logging backtrace location. When a backtrace location
is set and a log message is emitted at that location, the stack
of the goroutine executing the log statement will be printed to `stderr`.

| Client  | Method invocation                                     |
| :-----: | ----------------------------------------------------- |
| Console | `debug.backtraceAt(location)`                         |
|   RPC   | `{"method": "debug_backtraceAt", "params": [string]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| location | string | The logging backtrace location specified as `<filename>:<line>`. |

**Return Value**

None

**Example**

``` javascript
> debug.backtraceAt("server.go:443")
null
```


### debug_blockProfile

Turns on block profiling for the given duration and writes
profile data to disk. It uses a profile rate of 1 for most
accurate information. If a different rate is desired, set
the rate and write the profile manually using
`debug_writeBlockProfile`.

| Client  | Method invocation                                            |
| :-----: | ------------------------------------------------------------ |
| Console | `debug.blockProfile(file, seconds)`                          |
|   RPC   | `{"method": "debug_blockProfile", "params": [string, number]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| file | string | The filename for the profiling result. |
| seconds | int | The profiling duration in seconds. |

**Return Value**

None

**Example**

Console
```javascript
> debug.blockProfile("block.profile", 10)
null
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_blockProfile","params":["block.profile", 10],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":null}
```


### debug_cpuProfile

Turns on CPU profiling for the given duration and writes
profile data to disk.

| Client  | Method invocation                                            |
| :-----: | ------------------------------------------------------------ |
| Console | `debug.cpuProfile(file, seconds)`                            |
|   RPC   | `{"method": "debug_cpuProfile", "params": [string, number]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| file | string | The filename for the profiling result. |
| seconds | int | The profiling duration in seconds. |

**Return Value**

None

**Example**

Console
```javascript
> debug.cpuProfile("block.profile", 10)
null
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_cpuProfile","params":["block.profile", 10],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":null}
```


### debug_dumpBlock

Retrieves the state that corresponds to the block number and returns a list of accounts (including
storage and code).

**NOTE**: This function returns the state correctly for some latest block
numbers.  On the other hand, old block numbers that can be used to retrieve the
state may be restricted depending on the value set for the `klay` command-line
option `--state.block-interval` (default: 128).  This means that the function
will perform the state retrieval against only the block numbers, which are a
multiple of state.block-interval value.  For example, when the value of
state.block-interval is 128, this function returns the state correctly for the
block numbers such as "0x0", "0x80", "0x100", "0x180", and so on.  If the block
number is not a multiple of state.block-interval value, it will return 'missing
trie node' error.

| Client  | Method invocation                                   |
| :-----: | --------------------------------------------------- |
| Console | `debug.dumpBlock(number)`                           |
|   RPC   | `{"method": "debug_dumpBlock", "params": [number]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| number | string | The block number as a hex string. |

**Return Value**

| Type | Description |
| --- | --- |
| JSON string | The block information. |

**Example**

Console
```javascript
> debug.dumpBlock("0x80")
{
  accounts: {
    0000000000000000000000000000000000000035: {
      balance: "12800000000000000000",
      code: "6080604052600436106100615763ffffffff7c01000000000000000000000000000000000000000000000000000000006000350416631a39d8ef81146100805780636353586b146100a757806370a08231146100ca578063fd6b7ef8146100f8575b3360009081526001602052604081208054349081019091558154019055005b34801561008c57600080fd5b5061009561010d565b60408051918252519081900360200190f35b6100c873ffffffffffffffffffffffffffffffffffffffff60043516610113565b005b3480156100d657600080fd5b5061009573ffffffffffffffffffffffffffffffffffffffff60043516610147565b34801561010457600080fd5b506100c8610159565b60005481565b73ffffffffffffffffffffffffffffffffffffffff1660009081526001602052604081208054349081019091558154019055565b60016020526000908152604090205481565b336000908152600160205260408120805490829055908111156101af57604051339082156108fc029083906000818181858888f193505050501561019c576101af565b3360009081526001602052604090208190555b505600a165627a7a723058201307c3756f4e627009187dcdbc0b3e286c13b98ba9279a25bfcc18dd8bcd73e40029",
      codeHash: "62b00472fac99d94ccc52f5addac43d54c129cd2c6d2357c9557abea67efdec5",
      nonce: 0,
      root: "56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
      storage: {}
    },
    /...(skipped).../
      codeHash: "3f34b5d7038ae652086ba4847ede2668b26a50107c5258d1412f764b942e2661",
      nonce: 1,
      root: "56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
      storage: {}
    }
  },
  root: "70383c826d1161ec2f12d799023317d8da7775dd47b8502d2d7ef646d094d3a5"
}

> debug.dumpBlock("0x81")
Error: missing trie node a573119868e0898fe5526732a079dd713c005fcbcce38dec5cae75af0378e4d3 (path )
    at web3.js:3239:20
    at web3.js:6447:15
    at web3.js:5181:36
    at <anonymous>:1:1
```

HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_dumpBlock","params":["0x80"],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":{"root":"70383c826d1161ec2f12d799023317d8da7775dd47b8502d2d7ef646d094d3a5","accounts":{"0000000000000000000000000000000000000035":{"balance":"12800000000000000000","nonce":0,"root":"56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421","codeHash":"62b00472fac99d94ccc52f5addac43d54c129cd2c6d2357c9557abea67efdec5","code":"6080604052600436106100615763ffffffff7c01000000000000000000000000000000000000000000000000000000006000350416631a39d8ef81146100805780636353586b146100a757806370a08231146100ca578063fd6b7ef8146100f8575b3360009081526001602052604081208054349081019091558154019055005b34801561008c57600080fd5b5061009561010d565b60408051918252519081900360200190f35b6100c873ffffffffffffffffffffffffffffffffffffffff60043516610113565b005b3480156100d657600080fd5b5061009573ffffffffffffffffffffffffffffffffffffffff60043516610147565b34801561010457600080fd5b506100c8610159565b60005481565b73ffffffffffffffffffffffffffffffffffffffff1660009081526001602052604081208054349081019091558154019055565b60016020526000908152604090205481565b336000908152600160205260408120805490829055908111156101af57604051339082156108fc029083906000818181858888f193505050501561019c576101af565b3360009081526001602052604090208190555b505600a165627a7a723058201307c3756f4e627009187dcdbc0b3e286c13b98ba9279a25bfcc18dd8bcd73e40029","storage":{}},...(skipped)...}}}
```

### debug_gcStats

Returns GC statistics.

| Client  | Method invocation                                 |
|:-------:|---------------------------------------------------|
| Console | `debug.gcStats()`                                 |
| RPC     | `{"method": "debug_gcStats", "params": []}`       |

**Parameters**

None

**Return Value**

See [https://golang.org/pkg/runtime/debug/#GCStats](https://golang.org/pkg/runtime/debug/#GCStats)
for information about the fields of the returned object.

**Example**

Console
```javascript
> debug.gcStats()
{
  LastGC: "2018-10-08T14:27:34.030659164Z",
  NumGC: 40,
  Pause: [4582691, 2863122, 1578188, 459447, 197699, 1688008, 678298, 117924, 6871625, 298523, 1281135, 616022, 2513029, 265029, 704801, 685752, 383625, 677657, 1135878, 1730593, 603201, 2667691, 1901266, 1035406, 1624233, 1112526, 397637, 158158, 596931, 2675197, 4421722, 745039, 847417, 4423680, 653433, 281915, 605418, 8127664, 138283, 1810200],
  PauseEnd: ["2018-10-08T14:27:34.030659164Z", "2018-10-08T14:25:34.032422737Z", "2018-10-08T14:23:34.015065773Z", "2018-10-08T14:21:34.031893519Z", "2018-10-08T14:19:33.791324489Z", "2018-10-08T14:19:01.028883257Z", "2018-10-08T14:17:01.054270356Z", "2018-10-08T14:15:01.032846304Z", "2018-10-08T14:13:01.07313761Z", "2018-10-08T14:11:01.042653342Z", "2018-10-08T14:09:01.034458873Z", "2018-10-08T14:07:01.022701083Z", "2018-10-08T14:05:01.046456415Z", "2018-10-08T14:03:01.030075376Z", "2018-10-08T14:01:01.027838941Z", "2018-10-08T13:59:01.033363592Z", "2018-10-08T13:57:01.032853252Z", "2018-10-08T13:55:01.06825876Z", "2018-10-08T13:53:01.071426346Z", "2018-10-08T13:51:01.099618831Z", "2018-10-08T13:49:01.016314071Z", "2018-10-08T13:47:01.031721975Z", "2018-10-08T13:45:01.029069094Z", "2018-10-08T13:43:01.026156953Z", "2018-10-08T13:41:01.019240344Z", "2018-10-08T13:39:01.026699743Z", "2018-10-08T13:37:01.02642045Z", "2018-10-08T13:35:00.997556262Z", "2018-10-08T13:33:01.023605742Z", "2018-10-08T13:31:01.025436347Z", "2018-10-08T13:29:01.024808072Z", "2018-10-08T13:27:01.01508169Z", "2018-10-08T13:25:01.023461518Z", "2018-10-08T13:23:01.0144789Z", "2018-10-08T13:21:01.022121597Z", "2018-10-08T13:19:30.748905748Z", "2018-10-08T13:19:29.588457028Z", "2018-10-08T13:19:29.461820065Z", "2018-10-08T13:19:29.436792038Z", "2018-10-08T13:19:29.408990947Z"],
  PauseQuantiles: null,
  PauseTotal: 64156063
}
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_gcStats","params":[],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":{"LastGC":"2018-10-15T00:42:08.2787037Z","NumGC":14,"PauseTotal":292805500,"Pause":[3384700,60164200,259500,354600,62331200,241700,29701500,4868200,8242800,35177700,27621100,12647400,38250100,9560800],"PauseEnd":["2018-10-15T00:42:08.2787037Z","2018-10-15T00:40:19.3302813Z","2018-10-15T00:38:41.2202755Z","2018-10-15T00:36:41.2785669Z","2018-10-15T00:36:18.3196569Z","2018-10-15T00:34:48.2073609Z","2018-10-15T00:33:01.3309817Z","2018-10-15T00:31:28.3465898Z","2018-10-15T00:30:05.4245261Z","2018-10-15T00:28:58.6377593Z","2018-10-15T00:27:55.315809Z","2018-10-15T00:27:45.075085Z","2018-10-15T00:27:44.9164574Z","2018-10-15T00:27:44.8406572Z"],"PauseQuantiles":null}}
```


### debug_getBlockRlp

Retrieves and returns the RLP-encoded block by the block number.

| Client  | Method invocation                                     |
|:-------:|-------------------------------------------------------|
| Console | `debug.getBlockRlp(number)`                           |
| RPC     | `{"method": "debug_getBlockRlp", "params": [number]}` |

References: [RLP](https://github.com/ethereum/wiki/wiki/RLP)

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| number | int | The block number. |

**Return Value**

| Type | Description |
| --- | --- |
| string | The RLP-encoded block. |

**Example**

Console
```javascript
> debug.getBlockRlp(100)
"f90399f90394a05a825207c8396b848fefc73e442db004adee6596309af27630871b6a3d424758a01dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347940000000000000000000000000000000000000000940000000000000000000000000000000000000000a0b2ff1e4173123faa241fb93d83860e09f9e1ca1cfaf24c40c9e963e65c0b0317a056e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421a056e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421b9010000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000016485e8d4a50fff80845bb9e92eb90187d7820401846b6c617988676f312e31302e33856c696e75780000000000000000f90164f854943b215ed129645b949722d4efbd9c749838d85bf0947050164b7718c667c9661afd924f6c0c5e5d4a01947f303b360063efc575e99cf2f7602efa034e832e94f38624dba0e106aa6a79335f77d3fd6409f9e4d8b84126d1ae355905704d8ffcc50599a8a051ac7c50ed6fc6d7caf6510cf0329b56cf3e3babfe45cc95143074ca0385627ea3b6ac3f6ad7961b60f23e32965d3b0c2900f8c9b841c3423ecb41ee86b193dbb98bf74e0c1b8e0c475503a8f5ef37ef7566af34443c77b492a1f92e5a7411c36efeae08ebc698d02353c38f07a3d5c32168243ab7e901b841ec6558f4e5d123b9dc240e77db493f1e5e2f55f108d3c4f9b39e10dbca39ad7b3fc2dd5d27a7a3d92938ad4245bef5a914377fb2b92cbe342067a9963ab121b700b841f34ed94f29cd0aefd841cc8aba9dcc9d4c2fe14795f3a661e8ce92c2014c2099327e5f4285e1d1821e55f297cf5252bafed521ab49906b9b596a3187ce1e529c00a063746963616c2062797a616e74696e65206661756c7420746f6c6572616e6365880000000000000000c0c0"
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_getBlockRlp","params":['200'],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":"f905ebf905e6a03ab9a91f37811c1a2e975d1f684e4f9acbfcda09ff1529369a50a0c95ed0c026a01dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347940000000000000000000000000000000000000000940000000000000000000000000000000000000000a067302a0f946f9a676f187c0bebe6a61515321ff34614af507ac2fad9edac9018a056e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421a056e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421b90100000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000181c885e8d4a50fff80845bc3e058b903d8d7820302846b6c617988676f312e31302e34856c696e75780000000000000000f903b5f9011194011feb9f54d7cf0c59563d4f621a90e59fe839d9941aa5286f1ae8e183a8f8aebf2cb8425654a4979f94417a9f6e635f458be805702d85dd76d5dbb80aa8944eeabc5dbca48ff84a7fb36d67b51240ad37b01694518df54c51a355d1d961a641a1ee58a4acac62219453113c502402751deec24f6b4ca046878a74afa09459d4136ee1b30d9055b39675c86b5435d48662f9945b36da8763dce3bf84137ca79d56c468017383d2949b974f03aa0bb7d73882e3cfb80ec7a9e9ba543694af70cadbacb65d3a98352a12cc464dffb7e8181894c21dc40330bf91583c79eb58ae527487008f669d94c4a852238132f40ca085554f7bdcaf5f9ebab00f94ebd17315849780edc86f5f32081c8fbb0bd90a23b841ca7b5ac7fa0f6f228ba67544cf898005466a713bb863aaaaada20e58a838b2b468856e6277890a42ec92f8af40ca348a45438efa09d5e572ed8be4b535e016b701f9025bb841e64b232ed585759854e609c7947d19931a14e7906c5bbfe4acaa9aa7484d28f70b9dbfb7047dbd971e5d077d97306f26be258d2b016446fd41474082cc274a2b01b84195cf54ec0ac1ad4137be193f8d111f893590bd840cdb514c42ac169eb69e666a47e6cb358847364411c9cacb6261005d7fd4583e0d40216a1fa86fb083be270901b841e6caa2cb395a6e9d9c0853834a87a4c03444ec2b94035b3742c19ca09a72384c3ea9738aa66b43ad6f40269e8fd0ba015e23db36d7d7ccf666adba67c4bda4fc01b8418ad3f264ebb0dc47788f1d872423b7b067137d5d550f3311642308d5251f3e50049af533b8e16309ace3bf41ecf38a65a6e364458f8224c9efe57983c590590601b84129e147bf2666826af23027237e2894b074014ff38711ffe7168aa808a52c7e4863f2da1c5abd625567ef59b053f0b7abd36459b30a1e4d4d953784cb5f568fc500b841d58b713b28dba4bcb26ebbd274bbb12a8738a054c9cd18a2ee7298264e683a720dd633b66fa98edfe926e844d6e45f884b5e22fcd0df39d4d6f1fc84f1caceb701b8412dfaeadac01129c1ff9352c1b4b66e1c6b035b80e4b53e3ea5344a82ef607e3321fa05e57f6bdb27e4bd665af3e6c8b19575b3888f1b949c0cadbb186857b7cb01b841bb240246258aae93db40017e0ba437e0c4e517a25643282ee361f89fdb877a745f682a1f2c0441aa2d59cfa2058dc61d46f046499d4036efa02557b33a2f395200b841fdb9bb602e321d80ea09621e6ac36c01dc11152651404b4f7dba8be13466142914d97592c2625d83c27e220521d860b6128a0172c0ff39cdc23dabbfd4fdbb8100a063746963616c2062797a616e74696e65206661756c7420746f6c6572616e6365880000000000000000c0c0"}
```



### debug_goTrace

Turns on Go runtime tracing for the given duration and writes
trace data to disk.

| Client  | Method invocation                                         |
|:-------:|-----------------------------------------------------------|
| Console | `debug.goTrace(file, seconds)`                            |
| RPC     | `{"method": "debug_goTrace", "params": [string, number]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| file | string | The filename for the trace output. |
| seconds | int | The tracing duration in seconds. |

**Return Value**

None

**Example**

Console
```javascript
> debug.goTrace("go.trace", 5)
null
```
HTTP RPC

```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_goTrace","params":["go.trace",5],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":null}
```

### debug_memStats

Returns detailed runtime memory statistics.

| Client  | Method invocation                                 |
|:-------:|---------------------------------------------------|
| Console | `debug.memStats()`                                |
| RPC     | `{"method": "debug_memStats", "params": []}`      |

**Parameters**

None

**Return Value**

See [https://golang.org/pkg/runtime/#MemStats](https://golang.org/pkg/runtime/#MemStats)
for information about the fields of the returned object.

**Example**

Console
```javascript
> debug.memStats()
{
  Alloc: 132244280,
  BuckHashSys: 1922010,
  BySize: [{
      Frees: 0,
      Mallocs: 0,
      Size: 0
  }, {
      Frees: 496599,
      Mallocs: 499580,
      Size: 8
  }, 
  ...
  StackSys: 1195456,
  Sys: 107909880,
  TotalAlloc: 2105944960
}
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_memStats","params":[],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":{"Alloc":265525152,"TotalAlloc":3548997112,"Sys":756177144,"Lookups":2165,"Mallocs":25572268,"Frees":24933943,
...
"Frees":36},{"Size":16384,"Mallocs":123,"Frees":122},{"Size":18432,"Mallocs":11,"Frees":3},{"Size":19072,"Mallocs":2,"Frees":1}]}}
```

### debug_setHead

**`WARNING`**: This API is not yet implemented and always returns "not yet implemented API" error.

Sets the current head of the local chain by block number.

**NOTE**: This is a destructive action and may severely damage your chain.
Use with *extreme* caution.

| Client  | Method invocation                                 |
|:-------:|---------------------------------------------------|
| Console | `debug.setHead(number)`                           |
| RPC     | `{"method": "debug_setHead", "params": [number]}` |


**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| number | int | The block number. |

**Return Value**

None

**Example**

Console
```javascript
> debug.setHead("0x100")
null
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_setHead","params":["0x100"],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":null}
```


### debug_setBlockProfileRate

Sets the rate (in samples/sec) of goroutine block profile
data collection. A non-zero rate enables block profiling,
setting it to zero stops the profile. Collected profile data
can be written using `debug_writeBlockProfile`.

| Client  | Method invocation                                             |
|:-------:|---------------------------------------------------------------|
| Console | `debug.setBlockProfileRate(rate)`                             |
| RPC     | `{"method": "debug_setBlockProfileRate", "params": [number]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| rate | int | The profiling rate in samples/sec. |

**Return Value**

None

**Example**

Console
```javascript
> debug.setBlockProfileRate(1)
null
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_setBlockProfileRate","params":['3'],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":null}
```


### debug_setVMLogTarget

Sets the output target of vmlog precompiled contract.  When the output target
is a file, logs from `vmlog` calls in smart contracts will be written to
`DATADIR/log/vm.log`.  Here `DATADIR` is the directory specified by `--datadir`
when launching `klay`.  On the other hand, the output target is `stdout`, logs
will be displayed like a debug message on the standard output.

| Client  | Method invocation                                        |
|:-------:|----------------------------------------------------------|
| Console | `debug.setVMLogTarget(target)`                           |
| RPC     | `{"method": "debug_setVMLogTarget", "params": [number]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| target | int | The output target (0: no output, 1: file, 2: stdout, 3: both) (default: 0) |

**Return Value**

| Type | Description |
| --- | --- |
| string | The output target.  See the examples below for the actual return values. |

**Example**

Console
```javascript
> debug.setVMLogTarget(0)
"no output"

> debug.setVMLogTarget(1)
"file"

> debug.setVMLogTarget(2)
"stdout"

> debug.setVMLogTarget(3)
"both file and stdout"

> debug.setVMLogTarget(4)
Error: target should be between 0 and 3
    at web3.js:3239:20
    at web3.js:6447:15
    at web3.js:5181:36
    at <anonymous>:1:1
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_setVMLogTarget","params":[3],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":"both file and stdout"}
```


### debug_stacks

Returns a printed representation of the stacks of all goroutines.
Note that the web3 wrapper for this method takes care of the printing
and does not return the string.

| Client  | Method invocation                                 |
|:-------:|---------------------------------------------------|
| Console | `debug.stacks()`                                  |
| RPC     | `{"method": "debug_stacks", "params": []}`        |

**Parameters**

None

**Return Value**

| Type | Description |
| --- | --- |
| string | The stack information of all goroutines. |

**Example**

Console
```javascript
> debug.stacks()
goroutine 163577 [running]:
/api/debug.(*HandlerT).Stacks(0xc4200a4780, 0x0, 0x0)
	/klaytn/build/_workspace/src/github.com/klaytn/klaytn/api/debug/api.go:173 +0x74
reflect.Value.call(0xc4213a80c0, 0xc42134d1f0, 0x13, 0xf050ec, 0x4, 0xc4233957a0, 0x1, 0x1, 0x474401, 0xc4233956c8, ...)
...
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_stacks","params":[],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":"goroutine 76176 [running]:\ngithub.com/klaytn/klaytn/api/debug.(*HandlerT).Stacks(0xc0002ce050, 0x0, 0x0)\n\t/private/tmp/klaytn-20181001-13887-zbyv2z/build/_workspace/src/github.com/klaytn/klaytn/api/debug/api.go:173 +0x74\nreflect.Value.call(0xc01867c660, 0xc000231bd8, 0x13, 0x4b26ca7, 0x4, 0xc008d8b7c0, 0x1, 0x1, 0x30, 0xc0323211d0 ..."}
```
### debug_startCPUProfile

Turns on CPU profiling indefinitely, writing to the given file.

| Client  | Method invocation                                         |
|:-------:|-----------------------------------------------------------|
| Console | `debug.startCPUProfile(file)`                             |
| RPC     | `{"method": "debug_startCPUProfile", "params": [string]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| file | string | The filename for the profiling output. |

**Return Value**

None

**Example**

Console

```javascript
> debug.startCPUProfile("cpu.profile")
null
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_startCPUProfile","params":["cpu.profile"],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":null}
```


### debug_startGoTrace

Starts writing a Go runtime trace to the given file.

| Client  | Method invocation                                      |
|:-------:|--------------------------------------------------------|
| Console | `debug.startGoTrace(file)`                             |
| RPC     | `{"method": "debug_startGoTrace", "params": [string]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| file | string | The filename for the tracing output. |

**Return Value**

None

**Example**

Console
```javascript
> debug.startGoTrace("go.trace")
null
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_startGoTrace","params":["go.trace"],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":null}
```


### debug_stopCPUProfile

Stops an ongoing CPU profile.

| Client  | Method invocation                                  |
|:-------:|----------------------------------------------------|
| Console | `debug.stopCPUProfile()`                           |
| RPC     | `{"method": "debug_stopCPUProfile", "params": []}` |

**Parameters**

None

**Return Value**

None

**Example**

Console
```javascript
> debug.stopCPUProfile()
null
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_stopCPUProfile","params":[],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":null}
```


### debug_stopGoTrace

Stops writing the Go runtime trace.

| Client  | Method invocation                                 |
|:-------:|---------------------------------------------------|
| Console | `debug.stopGoTrace()`                             |
| RPC     | `{"method": "debug_stopGoTrace", "params": []}`   |

**Parameters**

None

**Return Value**

None

**Example**

Console
```javascript
> debug.stopGoTrace()
null
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_stopGoTrace","params":[],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":null}
```


### debug_verbosity

Sets the logging verbosity ceiling. Log messages with level 
up to and including the given level will be printed.

(Level :  0=silent, 1=error, 2=warn, 3=info, 4=debug, 5=detail)

The verbosity of individual packages and source files
can be raised using `debug_vmodule`.

| Client  | Method invocation                                 |
|:-------:|---------------------------------------------------|
| Console | `debug.verbosity(level)`                          |
| RPC     | `{"method": "debug_vmodule", "params": [number]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| level | int | The logging verbosity level. |

**Return Value**

None

**Example**

Console
```javascript
> debug.verbosity(3)
null
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_verbosity","params":['3'],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":null}
```


### debug_vmodule

Sets the logging verbosity pattern.

| Client  | Method invocation                                 |
|:-------:|---------------------------------------------------|
| Console | `debug.vmodule(module)`                           |
| RPC     | `{"method": "debug_vmodule", "params": [string]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| module | string | The module name for logging. |

**Return Value**

None

**Example**

Console

If you want to see messages from a particular Go package (directory)
and all subdirectories, use

```javascript
> debug.vmodule("p2p/*=5")
```

If you want to restrict messages to a particular package (*e.g.*, p2p)
but exclude subdirectories, use

```javascript
> debug.vmodule("p2p=4")
```

If you want to see log messages from a particular source file, use

```javascript
> debug.vmodule("server.go=3")
```
[//]: # "You can compose these basic patterns.If you want to see all"
[//]: # "output from peer.go in a package below klay (network/peer.go,"
[//]: # "networks/*/peer.go) as well as output from package p2p"
[//]: # "at level <= 5, use:"
[//]: # "```javascript"
[//]: # "debug.vmodule("network/*/peer.go=6,p2p=5")"
[//]: # "```"

HTTP RPC

```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_vmodule","params":["p2p=4"],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":null}
```

### debug_writeBlockProfile

Writes a goroutine blocking profile to the given file.

| Client  | Method invocation                                           |
|:-------:|-------------------------------------------------------------|
| Console | `debug.writeBlockProfile(file)`                             |
| RPC     | `{"method": "debug_writeBlockProfile", "params": [string]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| file | string | The filename for the profiling output. |

**Return Value**

None

**Example**

Console
```javascript
> debug.writeBlockProfile("block.profile")
null
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_writeBlockProfile","params":["block.profile"],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":null}
```



### debug_writeMemProfile

Writes an allocation profile to the given file.
Note that the profiling rate cannot be set through the API,
it must be set on the command line using the `--memprofilerate`
flag.

| Client  | Method invocation                                           |
|:-------:|-------------------------------------------------------------|
| Console | `debug.writeMemProfile(file)`                               |
| RPC     | `{"method": "debug_writeMemProfile", "params": [string]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| file | string | The filename for the profiling output. |

**Return Value**

None

**Example**

Console
```javascript
> debug.writeMemProfile("mem.profile")
null
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_writeMemProfile","params":["mem.profile"],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":null}
```


## Personal

The `personal` API manages private keys in the key store.


### personal_importRawKey

Imports the given unencrypted private key (hex string) into the key store,
encrypting it with the passphrase.

Returns the address of the new account.

| Client    | Method invocation                                                 |
| :-------: | ----------------------------------------------------------------- |
| Console   | `personal.importRawKey(keydata, passphrase)`                      |
| RPC       | `{"method": "personal_importRawKey", "params": [string, string]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| keydata | string | The unencrypted private key (hex string). |
| passphrase | string | The pass phrase for encryption. |

**Return Value**

| Name | Type | Description |
| --- | --- | --- |
| address | string | The account address to import. |

**Example**

Console
```javascript
> personal.importRawKey('9909aabe12ab3610f3dcbfb9fc545a9afa6a65b89602c0e610e9447afad9ca4a', 'mypassword')
"0xfa415bb3e6231f488ff39eb2897db0ef3636dd32"
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"personal_importRawKey","params":["9909aabe12ab3610f3dcbfb9fc545a9afa6a65b89602c0e610e9447afadcca4a", "mypassword"],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":"0xda04fb00e2cb5745cef7d8c4464378202a1673ef"}
```

### personal_listAccounts

Returns all the Klaytn account addresses of all keys
in the key store.

| Client    | Method invocation                                   |
| :-------: | --------------------------------------------------- |
| Console   | `personal.listAccounts`                             |
| RPC       | `{"method": "personal_listAccounts", "params": []}` |

**Parameters**

None

**Return Value**

| Type | Description |
| --- | --- |
| string | The list of all the Klaytn account addresses |

None

**Example**

Console
```javascript
> personal.listAccounts
["0x5e97870f263700f46aa00d967821199b9bc5a120", "0x3d80b31a78c30fc628f20b2c89d7ddbf6e53cedc"]
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"personal_listAccounts","params":[],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":["0xd8d81f52b595cc6135177c9c34ae6130ecad4636","0xda04fb00e2cb5745cef7d8c4464378202a1673ef"]}
```

### personal_lockAccount

Removes the private key with given address from memory.
The account can no longer be used to send transactions.

| Client    | Method invocation                                        |
| :-------: | -------------------------------------------------------- |
| Console   | `personal.lockAccount(address)`                          |
| RPC       | `{"method": "personal_lockAccount", "params": [string]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| address | string | The account address to lock. |

**Return Value**

| Type | Description |
| --- | --- |
| bool | `true` if the account was successfully locked, `false` otherwise. |

**Example**

Console
```javascript
> personal.lockAccount("0xfa415bb3e6231f488ff39eb2897db0ef3636dd32")
true
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"personal_lockAccount","params":["0xda04fb00e2cb5745cef7d8c4464378202a1673ef"],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":true}
```


### personal_newAccount

Generates a new private key and stores it in the key store directory.
The key file is encrypted with the given passphrase.
Returns the address of the new account.

At the Klaytn console, `newAccount` will prompt for a passphrase when
it is not supplied as the argument.

| Client    | Method invocation                                       |
| :-------: | ---------------------------------------------------     |
| Console   | `personal.newAccount(passphrase)`                       |
| RPC       | `{"method": "personal_newAccount", "params": [string]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| passphrase | string | (optional) the pass phrase used for encryption. |

**Return Value**

| Type | Description |
| --- | --- |
| string | The address of the new account. |

**Example**

Console
``` javascript
> personal.newAccount()
Passphrase:
Repeat passphrase:
"0x5e97870f263700f46aa00d967821199b9bc5a120"
```

The passphrase can also be supplied as a string.

``` javascript
> personal.newAccount("h4ck3r")
"0x3d80b31a78c30fc628f20b2c89d7ddbf6e53cedc"
```
HTTP RPC

```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"personal_newAccount","params":["helloWorld"],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":"0xed1b12248aee85a32aead06c7789d3fcdcd4dae6"}
```

### personal_unlockAccount

Decrypts the key with the given address from the key store.

Both passphrase and unlock duration are optional when using the JavaScript console.
If the passphrase is not supplied as an argument, the console will prompt for
the passphrase interactively.

The unencrypted key will be held in memory until the unlock duration expires.
If the unlock duration defaults to 300 seconds. An explicit duration
of zero seconds unlocks the key until the Klaytn local node exits.

The account can be used with `klay_sign` and `klay_sendTransaction` while it is unlocked.

| Client    | Method invocation                                                          |
| :-------: | -------------------------------------------------------------------------- |
| Console   | `personal.unlockAccount(address, passphrase, duration)`                    |
| RPC       | `{"method": "personal_unlockAccount", "params": [string, string, number]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| address | string | The account address to unlock. |
| passphrase | string | the pass phrase used for the encryption. |
| duration | int | (optional) the unlock duration (default to 300 seconds). |

**Return Value**

| Type | Description |
| --- | --- |
| bool | `true` if unlocked, `false` otherwise |

**Example**

Console
``` javascript
> personal.unlockAccount("0x5e97870f263700f46aa00d967821199b9bc5a120")
Unlock account 0x5e97870f263700f46aa00d967821199b9bc5a120
Passphrase:
true
```

Supplying the passphrase and unlock duration as arguments:

``` javascript
> personal.unlockAccount("0x5e97870f263700f46aa00d967821199b9bc5a120", "foo", 30)
true
```

If you want to type in the passphrase and stil override the default unlock duration,
pass `null` as the passphrase.

```
> personal.unlockAccount("0x5e97870f263700f46aa00d967821199b9bc5a120", null, 30)
Unlock account 0x5e97870f263700f46aa00d967821199b9bc5a120
Passphrase:
true
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"personal_unlockAccount","params":["0xda04fb00e2cb5745cef7d8c4464378202a1673ef","mypassword"],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":true}
```




### personal_sendTransaction

Validates the given passphrase and submits a transaction.

The transaction is the same argument as for `klay_sendTransaction` and contains the `from` address. If the passphrase can be used to decrypt the private key belogging to `tx.from` the transaction is verified, signed and send onto the network. The account is not unlocked globally in the node and cannot be used in other RPC calls.

| Client    | Method invocation                                                |
| :-------: | -----------------------------------------------------------------|
| Console   | `personal.sendTransaction(tx, passphrase)`                       |
| RPC       | `{"method": "personal_sendTransaction", "params": [tx, string]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| tx | string | A transaction. |
| passphrase | string | The pass phrase used for encryption. |

**Return Value**

| Type | Description |
| --- | --- |
| bool | `true` if unlocked, `false` otherwise. |

**Example**

Console
``` javascript
> var tx = {from: "0x391694e7e0b0cce554cb130d723a9d27458f9298", to: "0xafa3f8684e54059998bc3a7b0d2b0da075154d66", value: web3.toPeb(1.23, "KLAY")}
undefined
> personal.sendTransaction(tx, "passphrase")
0x8474441674cdd47b35b875fd1a530b800b51a5264b9975fb21129eeb8c18582f
```
HTTP RPC

**NOTE**: The function `web3.toPeb()` is not excutable in HTTP RPC.
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"personal_sendTransaction","params":[{"from":"0x1d4e05bb72677cb8fa576149c945b57d13f855e4","to":"0xafa3f8684e54059998bc3a7b0d2b0da075154d66","value":"0x1230000000"},"1234"],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":"0x26a7a8ba619a5e3e4d742c217f55f49591a5616b200c976bd58a966a05e294b7"}
```


### personal_sign

The `sign` method calculates a Klaytn-specific signature with:
`sign(keccak256("\x19Klaytn Signed Message:\n" + len(message) + message)))`.

Adding a prefix to the message makes the calculated signature recognisable as a Klaytn-specific signature. This prevents misuse where a malicious DApp can sign arbitrary data (*e.g.*, transaction) and use the signature to impersonate the victim.

See `personal_ecRecover` to verify the signature.

| Client  | Method invocation                                     |
|:-------:|-------------------------------------------------------|
| Console | `personal.sign(message, account, password)`                |
| RPC     | `{"method": "personal_sign", "params": [message, account, password]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| message | string | A message to sign. |
| account | string | The account address. |
| password | string | (optional) the pass phrase used for signing. |

**Return Value**

| Type | Description |
| --- | --- |
| string | A signature. |

**Example**

Console
``` javascript
> personal.sign("0xdeadbeaf", "0x9b2055d370f73ec7d8a03e965129118dc8f5bf83", "")
"0xa3f20717a250c2b0b729b7e5becbff67fdaef7e0699da4de7ca5895b02a170a12d887fd3b17bfdce3481f10bea41f45ba9f709d39ce8325427b57afcfc994cee1b"
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"personal_sign","params":["0xdead","0xda04fb00e2cb5745cef7d8c4464378202a1673ef","mypassword"],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":"0xccb8cce176b01fdc8f7ac3c101b8eb3b9005e938a60800e517624419dd8b7fba0e4598bdf1c4fa1743e1288e89b8b7090cc11f4b3640aafcbc71896ec73eec241b"}
```


### personal_ecRecover

`ecRecover` returns the address associated with the private key that was used to calculate the signature in `personal_sign`.

| Client  | Method invocation                                     |
|:-------:|-------------------------------------------------------|
| Console | `personal.ecRecover(message, signature)`                 |
| RPC     | `{"method": "personal_ecRecover", "params": [message, signature]}` |

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| message | string | A message. |
| signature | string | The signature. |

**Return Value**

| Type | Description |
| --- | --- |
| string | The account address. |

**Example**

Console

``` javascript
> personal.sign("0xdeadbeaf", "0x9b2055d370f73ec7d8a03e965129118dc8f5bf83", "")
"0xa3f20717a250c2b0b729b7e5becbff67fdaef7e0699da4de7ca5895b02a170a12d887fd3b17bfdce3481f10bea41f45ba9f709d39ce8325427b57afcfc994cee1b"
> personal.ecRecover("0xdeadbeaf", "0xa3f20717a250c2b0b729b7e5becbff67fdaef7e0699da4de7ca5895b02a170a12d887fd3b17bfdce3481f10bea41f45ba9f709d39ce8325427b57afcfc994cee1b")
"0x9b2055d370f73ec7d8a03e965129118dc8f5bf83"
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"personal_sign","params":["0xdead","0xda04fb00e2cb5745cef7d8c4464378202a1673ef","mypassword"],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":"0xccb8cce176b01fdc8f7ac3c101b8eb3b9005e938a60800e517624419dd8b7fba0e4598bdf1c4fa1743e1288e89b8b7090cc11f4b3640aafcbc71896ec73eec241b"}

$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"personal_ecRecover","params":["0xdead","0xccb8cce176b01fdc8f7ac3c101b8eb3b9005e938a60800e517624419dd8b7fba0e4598bdf1c4fa1743e1288e89b8b7090cc11f4b3640aafcbc71896ec73eec241b"],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":"0xda04fb00e2cb5745cef7d8c4464378202a1673ef"}
```

## Txpool

The `txpool` API gives you access to several non-standard RPC methods to inspect the contents of the
transaction pool containing all the currently pending transactions as well as the ones queued for
future processing.


### txpool_content

The `content` inspection property can be queried to list the exact details of all the transactions
currently pending for inclusion in the next block(s), as well as the ones that are being scheduled
for future execution only.

The result is an object with two fields `pending` and `queued`. Each of these fields are associative
arrays, in which each entry maps an origin-address to a batch of scheduled transactions. These batches
themselves are maps associating nonces with actual transactions.

**NOTE**: There may be multiple transactions associated with the same account and nonce. This can
happen if the user broadcasts multiple ones with varying gas allowances (or even completely different
transactions).

| Client  | Method invocation                                                       |
|:-------:|-------------------------------------------------------------------------|
| Console | `txpool.content`                                                        |
| RPC     | `{"method": "txpool_content"}`                                          |

**Parameters**

None

**Return Value**

| Type | Description |
| --- | --- |
| JSON string | The content of the transaction pool. |

**Example**

Console

```javascript
> txpool.content
{
  pending: {
    0x0216d5032f356960cd3749c31ab34eeff21b3395: {
      806: [{
        blockHash: "0x0000000000000000000000000000000000000000000000000000000000000000",
        blockNumber: null,
        from: "0x0216d5032f356960cd3749c31ab34eeff21b3395",
        gas: "0x5208",
        gasPrice: "0xba43b7400",
        hash: "0xaf953a2d01f55cfe080c0c94150a60105e8ac3d51153058a1f03dd239dd08586",
        input: "0x",
        nonce: "0x326",
        to: "0x7f69a91a3cf4be60020fb58b893b7cbb65376db8",
        transactionIndex: null,
        value: "0x19a99f0cf456000"
      }]
    },
    0x24d407e5a0b506e1cb2fae163100b5de01f5193c: {
      34: [{
        blockHash: "0x0000000000000000000000000000000000000000000000000000000000000000",
        blockNumber: null,
        from: "0x24d407e5a0b506e1cb2fae163100b5de01f5193c",
        gas: "0x44c72",
        gasPrice: "0x4a817c800",
        hash: "0xb5b8b853af32226755a65ba0602f7ed0e8be2211516153b75e9ed640a7d359fe",
        input: "0xb61d27f600000000000000000000000024d407e5a0b506e1cb2fae163100b5de01f5193c00000000000000000000000000000000000000000000000053444835ec580000000000000000000000000000000000000000000000000000000000000000006000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        nonce: "0x22",
        to: "0x7320785200f74861b69c49e4ab32399a71b34f1a",
        transactionIndex: null,
        value: "0x0"
      }]
    }
  },
  queued: {
    0x976a3fc5d6f7d259ebfb4cc2ae75115475e9867c: {
      3: [{
        blockHash: "0x0000000000000000000000000000000000000000000000000000000000000000",
        blockNumber: null,
        from: "0x976a3fc5d6f7d259ebfb4cc2ae75115475e9867c",
        gas: "0x15f90",
        gasPrice: "0x4a817c800",
        hash: "0x57b30c59fc39a50e1cba90e3099286dfa5aaf60294a629240b5bbec6e2e66576",
        input: "0x",
        nonce: "0x3",
        to: "0x346fb27de7e7370008f5da379f74dd49f5f2f80f",
        transactionIndex: null,
        value: "0x1f161421c8e0000"
      }]
    },
    0x9b11bf0459b0c4b2f87f8cebca4cfc26f294b63a: {
      2: [{
        blockHash: "0x0000000000000000000000000000000000000000000000000000000000000000",
        blockNumber: null,
        from: "0x9b11bf0459b0c4b2f87f8cebca4cfc26f294b63a",
        gas: "0x15f90",
        gasPrice: "0xba43b7400",
        hash: "0x3a3c0698552eec2455ed3190eac3996feccc806970a4a056106deaf6ceb1e5e3",
        input: "0x",
        nonce: "0x2",
        to: "0x24a461f25ee6a318bdef7f33de634a67bb67ac9d",
        transactionIndex: null,
        value: "0xebec21ee1da40000"
      }],
      6: [{
        blockHash: "0x0000000000000000000000000000000000000000000000000000000000000000",
        blockNumber: null,
        from: "0x9b11bf0459b0c4b2f87f8cebca4cfc26f294b63a",
        gas: "0x15f90",
        gasPrice: "0x4a817c800",
        hash: "0xbbcd1e45eae3b859203a04be7d6e1d7b03b222ec1d66dfcc8011dd39794b147e",
        input: "0x",
        nonce: "0x6",
        to: "0x6368f3f8c2b42435d6c136757382e4a59436a681",
        transactionIndex: null,
        value: "0xf9a951af55470000"
      }, {
        blockHash: "0x0000000000000000000000000000000000000000000000000000000000000000",
        blockNumber: null,
        from: "0x9b11bf0459b0c4b2f87f8cebca4cfc26f294b63a",
        gas: "0x15f90",
        gasPrice: "0x4a817c800",
        hash: "0x60803251d43f072904dc3a2d6a084701cd35b4985790baaf8a8f76696041b272",
        input: "0x",
        nonce: "0x6",
        to: "0x8db7b4e0ecb095fbd01dffa62010801296a9ac78",
        transactionIndex: null,
        value: "0xebe866f5f0a06000"
      }],
    }
  }
}
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"txpool_content","id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":{"pending":{},"queued":{}}}
#There is no pending transaction nor queued transaction.
```
### txpool_inspect

The `inspect` inspection property can be queried to list a textual summary of all the transactions
currently pending for inclusion in the next block(s), as well as the ones that are being scheduled
for future execution only. This is a method specifically tailored to developers to quickly see the
transactions in the pool and find any potential issues.

The result is an object with two fields `pending` and `queued`. Each of these fields is associative
arrays, in which each entry maps an origin-address to a batch of scheduled transactions. These batches
themselves are maps associating nonces with transactions summary strings.

**NOTE**: There may be multiple transactions associated with the same account and nonce. This can
happen if the user broadcast multiple ones with varying gas allowances (or even completely different
transactions).

| Client  | Method invocation                                              |
|:-------:|----------------------------------------------------------------|
| Console | `txpool.inspect`                                               |
| RPC     | `{"method": "txpool_inspect"}`                                 |

**Parameters**

None

**Return Value**

| Type | Description |
| --- | --- |
| JSON string | A list of pending and queued transactions. |

**Example**

Console
```javascript
> txpool.inspect
{
  pending: {
    0x26588a9301b0428d95e6fc3a5024fce8bec12d51: {
      31813: "0x3375ee30428b2a71c428afa5e89e427905f95f7e: 0 peb + 500000 gas × 25000000000 peb"
    },
    0x2a65aca4d5fc5b5c859090a6c34d164135398226: {
      563662: "0x958c1fa64b34db746925c6f8a3dd81128e40355e: 1051546810000000000 peb + 90000 gas × 25000000000 peb",
      563663: "0x77517b1491a0299a44d668473411676f94e97e34: 1051190740000000000 peb + 90000 gas × 25000000000 peb",
      563664: "0x3e2a7fe169c8f8eee251bb00d9fb6d304ce07d3a: 1050828950000000000 peb + 90000 gas × 25000000000 peb",
      563665: "0xaf6c4695da477f8c663ea2d8b768ad82cb6a8522: 1050544770000000000 peb + 90000 gas × 25000000000 peb",
      563666: "0x139b148094c50f4d20b01caf21b85edb711574db: 1048598530000000000 peb + 90000 gas × 25000000000 peb",
      563667: "0x48b3bd66770b0d1eecefce090dafee36257538ae: 1048367260000000000 peb + 90000 gas × 25000000000 peb"
    },
    0x9174e688d7de157c5c0583df424eaab2676ac162: {
      3: "0xbb9bc244d798123fde783fcc1c72d3bb8c189413: 30000000000000000000 peb + 85000 gas × 25000000000 peb"
    },
    0xb18f9d01323e150096650ab989cfecd39d757aec: {
      777: "0xcd79c72690750f079ae6ab6ccd7e7aedc03c7720: 0 peb + 1000000 gas × 25000000000 peb"
    },
    0xb2916c870cf66967b6510b76c07e9d13a5d23514: {
      2: "0x576f25199d60982a8f31a8dff4da8acb982e6aba: 26000000000000000000 peb + 90000 gas × 25000000000 peb"
    },
    0xbc0ca4f217e052753614d6b019948824d0d8688b: {
      0: "0x2910543af39aba0cd09dbb2d50200b3e800a63d2: 1000000000000000000 peb + 50000 gas × 25000000000 peb"
    },
    0xea674fdde714fd979de3edf0f56aa9716b898ec8: {
      70148: "0xe39c55ead9f997f7fa20ebe40fb4649943d7db66: 1000767667434026200 peb + 90000 gas × 25000000000 peb"
    }
  },
  queued: {
    0x0f6000de1578619320aba5e392706b131fb1de6f: {
      6: "0x8383534d0bcd0186d326c993031311c0ac0d9b2d: 9000000000000000000 peb + 21000 gas × 25000000000 peb"
    },
    0x5b30608c678e1ac464a8994c3b33e5cdf3497112: {
      6: "0x9773547e27f8303c87089dc42d9288aa2b9d8f06: 50000000000000000000 peb + 90000 gas × 25000000000 peb"
    },
    0x976a3fc5d6f7d259ebfb4cc2ae75115475e9867c: {
      3: "0x346fb27de7e7370008f5da379f74dd49f5f2f80f: 140000000000000000 peb + 90000 gas × 25000000000 peb"
    },
    0x9b11bf0459b0c4b2f87f8cebca4cfc26f294b63a: {
      2: "0x24a461f25ee6a318bdef7f33de634a67bb67ac9d: 17000000000000000000 peb + 90000 gas × 25000000000 peb",
      6: "0x6368f3f8c2b42435d6c136757382e4a59436a681: 17990000000000000000 peb + 90000 gas × 25000000000 peb",
      7: "0x6368f3f8c2b42435d6c136757382e4a59436a681: 17900000000000000000 peb + 90000 gas × 25000000000 peb"
    }
  }
}
```
HTTP RPC

```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"txpool_inspect","id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":{"pending":{"0x1A789E38cD567a00b7Fb8e1D39100ac395fa463B":{"0":"0x87AC99835e67168d4f9a40580f8F5C33550bA88b: 0 peb + 99000000 gas × 25000000000 peb"},"0xAb552FC3d76de919c74435A4C6B04576a9763934":{"0":"0x87AC99835e67168d4f9a40580f8F5C33550bA88b: 0 peb + 99000000 gas × 25000000000 peb"}},"queued":{}}}
```

### txpool_status

The `status` inspection property can be queried for the number of transactions currently pending for
inclusion in the next block(s), as well as the ones that are being scheduled for future execution only.

The result is an object with two fields `pending` and `queued`, each of which is a counter representing
the number of transactions in that particular state.

| Client  | Method invocation                             |
|:-------:|-----------------------------------------------|
| Console | `txpool.status`                               |
| RPC     | `{"method": "txpool_status"}`                 |

**Parameters**

None

**Return Value**

| Name | Type | Description |
| --- | --- | --- |
| pending | int | The number of pending transactions. |
| queued | int | The number of queued transactions. |

**Example**

Console

```javascript
> txpool.status
{
  pending: 10,
  queued: 7
}
```
HTTP RPC

```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"txpool_status","id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":{"pending":"0x0","queued":"0x0"}}
```
