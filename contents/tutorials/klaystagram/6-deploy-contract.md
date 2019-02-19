---
title: F. Deploy contract
sidebar: mydoc_sidebar_tutorial
permalink: klaystagram/6-deploy-contract.html
folder: mydoc
---


## F. Deploy contract

1) Get some KLAY to deploy contract
2) Truffle configuration  
2) Deploy setup (Which contract do you want to deploy?)  
3) Deploy  


### 1) Get some KLAY  
To deploy contract, we need some KLAY in your account to pay for gas price. You can get 1000 test KLAY via Klaytn Wallet in the testnet.
1. Create your Klaytn account at [KlaytnWallet](https://wallet.klaytn.com/create)  
-> `PRIVATE KEY` will be used in truffle configuration. So copy it down somewhere
2. After creating your Klaytn account, run faucet to receive 1000 KLAY in [KlaytnFaucet](https://wallet.klaytn.com/faucet)

![create-accout & run-klay-faucet](../../../images/klaystagram-run-faucet.png)

> &#9888; Klaytn Wallet is for testing.  
>The KLAY Faucet can only be used for development purpose by authorized IP.
If you need the KLAY faucet for development purpose, please contact us at contact@klaytn.com
### 2) truffle configuration  

`truffle.js` is a configuration file including deployment configuration. There are 2 different methods to deploy your contract.  

- DEPLOY METHOD 1: By private key
- [DEPLOY METHOD 2: By unlocked account](http://docs.klaytn.net/tutorials/7-deploy-contract.html#2-deploy-method-2-by-unlocked-account-difficult)

We are going to deploy our contract by using `private key` we've just created in the previous step.
*WARNING: You shouldn't expose your private key. Otherwise, your account would be hacked.*

```js
//truffle.js

const PrivateKeyConnector = require('connect-privkey-to-provider')

/**
 * truffle network variables
 * for deploying contract to klaytn network.
 */
const NETWORK_ID = '1000'
const GASLIMIT = '20000000'

/**
 * URL: URL for the remote node you will be using
 * PRIVATE_KEY: Private key of the account that pays for the transaction (Change it to your own private key)
 */
const URL = `http://aspen.klaytn.com`
const PRIVATE_KEY = '0x2c4078447e583b57f0666f0db32e14020aef12b02b2607409bfe35962d8f1aad'

module.exports = {
  networks: {
    klaytn: {
      provider: new PrivateKeyConnector(PRIVATE_KEY, URL),
      network_id: NETWORK_ID,
      gas: GASLIMIT,
      gasPrice: null,
    },
  },

  // Specify the version of compiler, we use 0.4.24
  compilers: {
    solc: {
      version: '0.4.24',
    },
  },
}
```

#### `networks` property
See `networks` property above. `klaytn` network has 4 properties,  
`provider`, `network_id`, `gas`, `gasPrice`.

* `provider: new PrivateKeyConnector(PRIVATE_KEY, URL)`  
Just as the name indicates, it injects private key and url defined above.  

* `network_id: NETWORK_ID`  
Specify network id in Klaytn, you should set it to `1000` to use Klaytn Aspen network (testnet).  

* `gas: GASLIMIT`  
Maximum gas you are willing to spend.   

* `gasPrice: null`  
This is price per every gas unit. Currently in Klaytn, the gas price is fixed to `'25000000000'`. By setting it to `null`, truffle will automatically set the gas price.

#### `compiler` property
Remember that for Solidity contract we used version 0.4.24, thus specify compiler version here.

### 3) Deployment setup
`migrations/2_deploy_contracts.js`:  

```js
const Klaystagram = artifacts.require('./Klaystagram.sol')
const fs = require('fs')

module.exports = function (deployer) {
  deployer.deploy(Klaystagram)
    .then(() => {
    // 1. Save abi file to 'deployedABI'
    if (Klaystagram._json) {
      fs.writeFile(
        'deployedABI',
        JSON.stringify(Klaystagram._json.abi, 2),
        (err) => {
          if (err) throw err
          console.log(`The abi of ${Klaystagram._json.contractName} is recorded on deployedABI file`)
        })
    }

    // 2. Save contract address to 'deployedAddress'
    fs.writeFile(
      'deployedAddress',
      Klaystagram.address,
      (err) => {
        if (err) throw err
        console.log(`The deployed contract address * ${Klaystagram.address} * is recorded on deployedAddress file`)
    })
  })
}
```

You can specify which contract code will you deploy in your `contracts/` directory.  

1. Import your contract file (`Klaystagram.sol`) via  
`const Klaystagram = artifacts.require('./Klaystagram.sol')`  
2. Use `deployer` to deploy your contract,  `deployer.deploy(Klaystagram)`.  
3. If you want to add more logic after deploying your contract, use `.then()` (optional)  
4. To save contracts' `deployedABI` and `deployedAddress`, use `fs` node.js module  
`fs.writeFile(filename, content, callback)` (optional)

cf. For further information about `artifacts.`, refer to truffle official documentation at https://truffleframework.com/docs/truffle/getting-started/running-migrations#artifacts-require-.


### 4) Deploy

![deploy-contract](../../../images/klaystgram-deploy-contract.png)  
In your terminal type `$ truffle deploy --network klaytn`.  
It will deploy your contract according to `truffle.js` and `migrations/2_deploy_contracts.js` configuration.  

cf) `--reset` option  
If you provide this option, truffle will recompile and redeploy your contract even if contracts haven't changed.  
ex) `$ truffle deploy --reset --network klaytn`

Terminal will display deployed contract address if deployment was successful.  

[Next: Frontend code overview](7-frontend-overview.md)
