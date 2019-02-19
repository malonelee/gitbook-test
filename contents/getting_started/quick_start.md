---
title: Quick Start
sidebar: mydoc_sidebar_gs
permalink: getting_started/quick_start.html
folder: mydoc
---
This page will help you build a basic smart contract from scratch. Smart contract is a protocol that can be used on Klaytn to conduct business logics, store data and send data to others. After deployed, it can be executed by calling it from other accounts. Once it is deployed, it will exist and be executable until the Klaytn network halts. Contracts for Klaytn are written in Solidity and then compiled into bytecode to be uploaded on Klaytn.

## Charging KLAY using KlaytnFaucet

Tutorial #1 shows how to access and create accounts in the Klaytn network with the Klaytn binary package. In this tutorial, you will learn how to create new Klaytn accounts and charge KLAY using KlaytnFaucet.

### Launching a Klaytn Node

Unzip the provided Klaytn binary package and copy the files into the klaytn folder.

**NOTE**: If you do not have the Klaytn binary package (klaytn_0.4.1.zip), first fill out [the application form](https://docs.google.com/forms/d/e/1FAIpQLSfIhAoGRKeCOkKSdbsQ6YiyKt4p6UesK2_IB_g47CnHzRwYxw/viewform) at [https://www.klaytn.com](https://www.klaytn.com) to get the package.

```shell
$ unzip klaytn_0.4.1.zip klaytn_0.4.1_1/klaytn_mac/* # enter klaytn_linux instead of klaytn_mac for linux
$ mkdir klaytn # make a new directory
$ mv klaytn_0.4.1_1/klaytn_mac/* ./klaytn # move files into klaytn folder
$ rm -rf klaytn_0.4.1_1 # remove the empty directory
$ cd klaytn
# Initialize and start a Klaytn node by the following commands.
$ ./init.sh
$ ./start.sh
```
### Checking the `klay` Process
To check that the Klaytn node runs well, execute the following command.
```shell
$ ps -ef | grep klay
501 29175     1   0  4:05PM ttys006    0:58.95 ./klay --srvtype http --dbtype leveldb --metrics --prometheus --verbosity 3 --txpool.accountslots 20000 --txpool.accountqueue 20000 --txpool.globalslots 40960 --txpool.globalqueue 40960 --networkid 1000 --nodiscover --wsorigins * --datadir data/dd --syncmode full --rpc --rpcapi admin,txpool,personal,klay,debug,net,web3 --ws --wsaddr 0.0.0.0 --wsport 8552 --rpcaddr 0.0.0.0 --rpccorsdomain * --rpcvhosts * --maxpeers 5000 --rpcport 8551 --port 32323
```

### Attaching to the Console
To attach to the javascript console of the Klaytn node, execute the following command.
```shell
$ ./klay attach data/dd/klay.ipc
Welcome to the Klaytn JavaScript console!

instance: Klaytn/vv0.4.1/darwin-amd64/go1.11
coinbase: 0x21436f099eb8881baff0c71f87067d0bd6948d5b
at block: 60552 (Tue, 02 Oct 2018 16:07:00 KST)
 datadir: /Users/Workspace/klaytn/data/dd
 modules: admin:1.0 debug:1.0 istanbul:1.0 klay:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0
>
```
**NOTE**: Type in `klay` or `personal` to get the lists of available functions.

### Creating a New Klaytn Account
To create a new Klaytn account using the javascript console, execute the following command.
```shell
> personal.newAccount()
Passphrase:  # enter your passphrase
Repeat passphrase:
'75a59b94889a05c03c66c3c84e9d2f8308ca4abd' # created account address
```

### Unlocking the Klaytn Account
To unlock the created account, execute the following command.
```shell
> personal.unlockAccount('75a59b94889a05c03c66c3c84e9d2f8308ca4abd') # account address to unlock
Unlock account 75a59b94889a05c03c66c3c84e9d2f8308ca4abd
Passphrase: # enter your passphrase
true
```

### Getting KLAY from the KLAY Faucet
There are two ways to charge KLAY to your account.
- Using the KLAY faucet.
1. Access [https://wallet.klaytn.com](https://wallet.klaytn.com).
2. You can either use the private key of your account or use a keystore file that you've just made. 
3. Request to charge KLAY on the KLAY faucet. You can get 1000 KLAY for each request.

- Using `curl`.

```shell
# enter your account address
$ curl "https://apiwallet.klaytn.com/faucet?address=75a59b94889a05c03c66c3c84e9d2f8308ca4abd"
```

### Checking the Balance in Your Account
To see the balance of your account, execute the following command.

The default unit is peb (1 KLAY = 10<sup>18</sup> peb). 
More information about KLAY units can be found at [Units of KLAY](/smart_contract/execution_model_and_runtime.html#units-of-klay).
```shell
> klay.getBalance('75a59b94889a05c03c66c3c84e9d2f8308ca4abd') # enter your account address
1e+21  # 1000 KLAY
```

### Exiting the Console
To lave the javascript console, execute the following command.
```shell
> exit
$
```

## Deploying a Smart Contract: KlaytnGreeter
Tutorial #2 shows how to create smart contracts and deploy them on the Klaytn network.
Before starting this tutorial, you need to install caver.js and Truffle.

### Installing caver.js
caver.js is a JSON RPC framework for the Klaytn network (equivalent to web3.js in Ethereum).
Before installing caver.js, you must generate `package.json` file via `npm init` command, and then type in `npm install caver-js` to install caver.js.

```shell
$ npm init # initialize npm at the klaytn directory
$ npm install caver-js
```

**NOTE**: If you already installed caver.js, please update it to the latest version.


```shell
$ npm cache clean --force # initialize npm cache
$ npm install caver.js@latest # update caver.js to the latest version
```

**NOTE**: APIs of caver.js are almost identical to those of web3.js of Ethereum, at the moment (2018-10-08).

**For all the commands that begin with `web3.eth...` in web3.js, should be replaced with `caver.klay...`.**

`web3.eth.sendTransaction({ ... })` (X)
`caver.klay.sendTransaction({ ... })` (O)

### Installing Truffle

In this tutorial, Truffle is used to compile and deploy smart contracts written
in Solidity.  For further information about Truffle, refer to the following
sites:
- Truffle repository - [https://github.com/trufflesuite/truffle](https://github.com/trufflesuite/truffle)
- Truffle documents - [https://truffleframework.com/docs](https://truffleframework.com/docs)

We can install Truffle either 1) globally using npm by executing the following
command:
```shell
$ sudo npm install -g truffle
```

or 2) locally, *i.e.*, in your local directory, by executing the following:
```shell
# Assuming you are in $HOME/klaytn/.
$ npm install truffle
$ ln -s node_modules/truffle/build/cli.bundled.js truffle
$ export PATH=`pwd`:$PATH
```

Now we are ready to develop and deploy Klaytn smart contracts!

### Creating a Project Directory
First of all, create a directory where the source code locates.
```shell
$ mkdir klaytn-testboard
$ cd klaytn-testboard
```

### Initializing Truffle

Initialize Truffle for contract deployment.
```
$ truffle init
```

### Writing a Simple Smart Contract in Solidity

Create `KlaytnGreeter.sol` at `klaytn-testboard/contracts` directory.
```shell
$ cd contracts
$ touch KlaytnGreeter.sol
$ open KlaytnGreeter.sol
```
Write the following code in KlaytnGreeter.sol.
```solidity
contract Mortal {
    /* Define variable owner of the type address */
    address owner;
    /* This function is executed at initialization and sets the owner of the contract */
    function Mortal() { owner = msg.sender; }
    /* Function to recover the funds on the contract */
    function kill() { if (msg.sender == owner) selfdestruct(owner); }
}

contract KlaytnGreeter is Mortal {
    /* Define variable greeting of the type string */
    string greeting;
    /* This runs when the contract is executed */
    function KlaytnGreeter(string _greeting) public {
        greeting = _greeting;
    }
    /* Main function */
    function greet() constant returns (string) {
        return greeting;
    }
}
```

### Modifying the Migration Script
```shell
$ cd ..
$ cd migrations
$ open 1_initial_migration.js
```
Modify `1_initial_migration.js` as the following.
```javascript
var Migrations = artifacts.require("./Migrations.sol");
var KlaytnGreeter = artifacts.require("./KlaytnGreeter.sol");
module.exports = function(deployer) {
  deployer.deploy(Migrations);
  deployer.deploy(KlaytnGreeter, 'Hello, Klaytn');
};
```

### Deploying a Smart Contract using Truffle

Enter Klaytn's network information into truffle.js.


**`WARNING`**: Currently Klaytn Aspen network's gasPrice is fixed to 25 Gpeb (**It returns an error if you attempt to use any other number**).

```shell
$ cd ..
$ open truffle.js
```
```javascript
// truffle.js
module.exports = {
    networks: {
        klaytn: {
            host: '127.0.0.1',
            port: 8551,
            from: '0xabcdabcdabcdabcdabcdabcdabcdabcdabcdabcd', // enter your publickey
            network_id: '1000', // Aspen network id
            gas: 20000000, // transaction gas limit
            gasPrice: 25000000000, // gasPrice of Aspen is 25 Gpeb
        },
    },
};
```
Deploy the contract using the following command.

**NOTE**: Use `--network` to select which network to deploy and `--reset` to overwrite.

**NOTE**: Make sure that your Klaytn node is running.
```shell
$ truffle deploy --network klaytn --reset
Using network 'klaytn'.
Running migration: 1_initial_migration.js
  Deploying Migrations...
  ... 0x0f5108bd9e51fe6bf71dfc472577e3f55519e0b5d140a99bf65faf26830acfca
  Migrations: 0x97b1b3735c8f2326a262dbbe6c574a8ea1ba0b7d
  Deploying KlaytnGreeter...
  ... 0xcba53b6090cb4a118359b27293ba95116a8f35f66ae50fbd23ae1081ce9ffb9e
  KlaytnGreeter: 0xc9d831b68ac490b810a60c05c3eb378e645b5241 # this is your smart contract address
Saving successful migration to network...
  ... 0x14eb68727ca5a0ac767441c9b7ab077336f9311f71e9854d42c617aebceeec72
Saving artifacts...
```


**`WARNING`**: It returns an error when your account is locked.


```shell
Running migration: 1_initial_migration.js
  Replacing Migrations...
  ... undefined
Error encountered, bailing. Network state unknown. Review successful transactions manually.
Error: authentication needed: password or unlock
```


This is how you unlock your account.

```javascript
> personal.unlockAccount('0x70bf4d9337a5abd96119fadc3c9681686373a4d8')
Unlock account 0x70bf4d9337a5abd96119fadc3c9681686373a4d8
Passphrase:
true 
```

And then you are ready to go. Try deploy again.




### Checking the Deployed Byte Code using caver.js

Use `getCode` for checking the byte code of the deployed smart contract.

First, create a test file and open it.
```shell
$ touch test-klaytn.js
$ open test-klaytn.js
```
Write the following test code.
```javascript
// test-klaytn.js
var Caver = require('caver-js');
var caver = new Caver('http://127.0.0.1:8551');
// enter your smart contract address
caver.klay.getCode('0xc9d831b68ac490b810a60c05c3eb378e645b5241').then(console.log);
```
Run the code.
```shell
$ node test-klaytn.js
0x60806040526004361061004c576000357c0100000000000000000000000000000000000000000000000000000000900463ffffffff16806341c0e1b514610051578063cfae321714610068575b600080fd5b34801561005d57600080fd5b506100666100f8565b005b34801561007457600080fd5b5061007d610189565b6040518080602001828103825283818151815260200191508051906020019080838360005b838110156100bd5780820151818401526020810190506100a2565b50505050905090810190601f1680156100ea5780820380516001836020036101000a031916815260200191505b509250505060405180...
```


### Calling functions in the Deployed Smart Contract

Use javascript to call the `greet()` in the contract.

**NOTE**: In order to call specific functions in smart contracts, you need an ABI (Application Binary Interface) file. When Truffle deploys your contract, it automatically creates .json file at `./build/contracts/` which contains `abi` property.

Append the following lines to the test code written above.
```javascript
// test-klaytn.js
var Caver = require('caver-js');
var caver = new Caver('http://127.0.0.1:8551');
// enter your smart contract address
caver.klay.getCode('0xc9d831b68ac490b810a60c05c3eb378e645b5241').then(console.log);
// add lines
var KlaytnGreeter = require('./build/contracts/KlaytnGreeter.json');
// enter your smart contract address
var klaytnGreeter = new caver.klay.Contract(KlaytnGreeter.abi, '0xc9d831b68ac490b810a60c05c3eb378e645b5241');
klaytnGreeter.methods.greet().call().then(console.log);
```
Run the test code.
```shell
$ node test-klaytn.js
0x60806040526004361061004c576000357c0100000000000000000000000000000000000000000000000000000000900463ffffffff16806341c0e1b514610051578063cfae321714610068575b600080fd5b34801561005d57600080fd5b506100666100f8565b005b34801561007457600080fd5b5061007d610189565b6040518080602001828103825283818151815260200191508051906020019080838360005b838110156100bd5780820151... # This is from caver.klay.getCode
Hello, Klaytn # This is from KlyatnGreeter.methods.greet()
```
**If you got "Hello, Klaytn", you've completed the task. Congrats!**

## Token Generation Standard
More detailed contents will be updated soon.

## Troubleshooting

- Where can I find log files for the running Klaytn node using the Klaytn binary package?
  - You can find the log file in `data/logs/` where you ran `start.sh`.

- I can't launch a Klaytn node using the Klaytn binary package.
  - If you see the following protocol stack error message in the log file, it means Klaytn failed to start because the full path name of current working directory is too long. Please launch a Klaytn node from a directory with a shorter full path name. The maximum allowed length of path name depends on operating system.
    ```
    Fatal: Error starting protocol stack: listen unix /Users/username/some_directory/more_directories/klaytn/klaytn_client/kaytn_mac/data/dd/klay.ipc: bind: invalid argument 
    ```
