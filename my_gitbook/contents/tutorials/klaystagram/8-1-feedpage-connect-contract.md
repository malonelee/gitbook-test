---
title: H-1. Connect contract
sidebar: mydoc_sidebar_tutorial
permalink: klaystagram/8-1-feedpage-connect-contract.html
folder: mydoc
---


## H-1. Connect contract to front-end

1. `src/klaytn`
    * caver.js
    * KlaystagramContract.js
2. `src/redux`


### 1) `src/klaytn`
`src/klaytn`: Contains files that help interact with Klaytn blockchain.
* `src/klaytn/caver.js`: Instantiates caver within configured setting.  
cf) caver-js is a RPC call library which makes a connection to klaytn node, interacting with node or smart contract deployed on Klaytn.
* `src/klaytn/Klaystagram.js`: Creates an instance of contract using caver-js API. You can interact with contract through the instance  


#### `caver.js`
```js
/**
 * caver-js library helps making connection with klaytn node.
 * You can connect to specific klaytn node by setting 'rpcURL' value.
 * default rpcURL is 'http://aspen.klaytn.com'.
 */
import Caver from 'caver-js'

export const config = {
  rpcURL: 'http://aspen.klaytn.com'
}

export const cav = new Caver(config.rpcURL)

export default cav
```

After making the connection, you can call methods on smart contract with caver.

#### `KlaystagramContract.js`
```js
import { cav } from 'klaytn/caver'

class KlaystagramContract {
  constructor() {
    // 1. Create contract instance
    // You can call contract methods through this instance.
    this.instance = DEPLOYED_ABI
      && DEPLOYED_ADDRESS
      && new cav.klay.Contract(DEPLOYED_ABI, DEPLOYED_ADDRESS)
  }

  static getInstance() {
    if (this.instance) return this.instance
    this.instance = new KlaystagramContract()
    return this.instance
  }
}

export default KlaystagramContract
```

To interact with contract, we need a contract instance created with deployed contract address. 

`KlaystagramContract` creates a contract instance to interact with Klaystagram contract, by providing `DEPLOYED_ABI` and `DEPLOYED_ADDRESS` to `cav.klay.Contract` API.

When compiling & deploying Klaystagram.sol contract ([F. Deploy contract](6-deploy-contract.md)), we already created `deployedABI` and `deployedAddress` files. They contain ABI of Klaystagram contract and deployed contract address.  
And thanks to [webpack's configuration](@TODO), we can access it as variable.(`DEPLOYED_ADDRESS`, `DEPLOYED_ABI`)

* `DEPLOYED_ADDRESS` returns deployed Address  
* `DEPLOYED_ABI` returns Klaystagram contract ABI

`contract ABI`(Application Binary Interface) is the interface for calling contract methods. With this interface, we can call contract methods as below
* `contractInstance.methods.methodName().call()`  
* `contractInstance.methods.methodName().send({ ... })`  

**Now we are ready to interact with contract in the application.**  

*cf. For more information, refer to  `caver.klay.Contract`. (https://docs.klaytn.com/api/toolkit.html#caverklaycontract)*


### 2) `src/redux`
We are going to make API functions with Klaystagram instance. After calling API functions, we use redux store to save data from contract.


1. Import contract instance  
By using `KlaystagramContract` instance, we can call contract's methods when components need to interact with contract.

2. Call contract method

3. Store data from contract    
If transaction is successful, we will call redux action to save information from contract to redux store.

**Redux store controls all data flow in front-end**

```js
// src/redux/photo.js

import contract from 'klaytn/KlaystagramContract'

/**
 * 1. Import contract instance
*/
const KlaystagramContract = contract.getInstance().instance

const setFeed = (feed) => ({
  type: SET_FEED,
  payload: { feed },
})

const updateFeed = (tokenId) => (dispatch, getState) => {
  /**
   * 2. Call contract method (CALL): getPhoto()
   * For example, if you want to call `getPhoto` function do it as below
  */
  KlaystagramContract.methods.getPhoto(tokenId).call()
    .then((newPhoto) => {
      const { photos: { feed } } = getState()
      const newFeed = [newPhoto, ...feed]
      /** 
       * 3. Dispaching setFeed action
       * to save transaction data to redux store
      */
      dispatch(setFeed(newFeed))
    })
}
```


[Next: UploadPhoto component](8-2-feedpage-uploadphoto.md)