---
title: H-4. TransferOwnership comopnent
sidebar: mydoc_sidebar_tutorial
permalink: klaystagram/8-4-feedpage-transferownership.html
folder: mydoc
---


## H-4. TransferOwnership component
![transfer ownership](../../../images/klaystagram-transferownership.png)

1. `TransferOwnership` component's role
2. Component code
    2-1. Rendering `transferOwnership` button
    2-2. `TransferOwnership` component
3. Interact with contract: `transferOwnership` method  
4. Update data to store: `updateOwnerAddress` action  

### 1) `TransferOwnership` component's role
The owner of photo can transfer photo's ownership to another user. By sending `transferOwnership` transaction, new owner's address will be saved in ownership history, which keep tracks of past owner addresses.

### 2) Component code

#### 2-1) Rendering `TransferOwnership` button
We are going to render `TransferOwnership` button on the `FeedPhoto` component only when photo's owner address matches with logged-in user's address (which means you are the owner).
```js
// src/components/FeedPhoto.js

<div className="FeedPhoto__actions">
  {
    userAddress === currentOwner && (
      <IconButton
        className="FeedPhoto__transferOwnership"
        icon="icon-transfer.png"
        alt="Transfer Ownership"
        onClick={() => ui.showModal({
          header: 'Transfer Ownership',
          content: (
            <TransferOwnership
              id={id}
              issueDate={issueDate}
              currentOwner={currentOwner}
            />
          ),
        })}
      />
    )
  }
  // ...
</div>
```

#### 2-2) `TransferOwnership` component
```js
// src/components/TransferOwnership.js

import React, { Component } from 'react'
import { connect } from 'react-redux'
import * as photoActions from 'redux/actions/photos'
import Input from 'components/Input'
import Button from 'components/Button'

import './TransferOwnership.scss'

class TransferOwnership extends Component {
  state = {
    to: null,
  }

  handleInputChange = (e) => {
    this.setState({
      [e.target.name]: e.target.value,
    })
  }

  handleSubmit = (e) => {
    e.preventDefault()
    const { id, transferOwnership } = this.props
    const { to } = this.state
    /** 
     * Write data to contract (Sending transction)
     * id: photo's token id
     * to: new owner's address
    */
    transferOwnership(id, to)
    ui.hideModal()
  }

  render() {
    const { id, issueDate, currentOwner } = this.props
    return (
      <div className="TransferOwnership">
        <div className="TransferOwnership__info">
          <p className="TransferOwnership__infoCopyright">Copyright. {id}</p>
          <p>Issue Date  {issueDate}</p>
        </div>
        <form className="TransferOwnership__form" onSubmit={this.handleSubmit}>
          <Input
            className="TransferOwnership__from"
            name="from"
            label="CURRENT OWNER"
            value={currentOwner}
            disabled
          />
          <Input
            className="TransferOwnership__to"
            name="to"
            label="NEW OWNER"
            onChange={this.handleInputChange}
            placeholder="Transfer Ownership to..."
            required
          />
          <Button
            className="TransferOwnership__transfer"
            type="submit"
            title="Transfer Ownership"
          />
        </form>
      </div>
    )
  }
}

const mapDispatchToProps = (dispatch) => ({
  transferOwnership: (id, to) => dispatch(photoActions.transferOwnership(id, to)),
})

export default connect(null, mapDispatchToProps)(TransferOwnership)
```

### 3) Interact with contract: `transferOwnership` method  
We already made `transferOwnership` function in Klaystagram contract at chapter [E. Write Smart Contract](5-write-smart-contract.md). Let's call it from application.

1. Call contract method (SEND): Send `transferOwnership` transaction
    * `id:` photo's tokenId
    * `to:` Address to tranfer photo's ownership
2. Set options 
    * `from:` account that sends this transaction (= who will pay for this transaction)  
    * `gas:` amount of maximum cost willing to pay for sending the transaction
3. After sending transaction, show progress with `Toast` component in trasaction life cycle
4. If transaction successfully gets into block, call `updateOwnerAddress` function to update feed  

```js
// src/redux/photo.js

export const transferOwnership = (tokenId, to) => (dispatch) => {
  /** 
   * 1. Call contract method (SEND): transferOwnership(id, to)
   * id: photo's token id
   * to: new owner's address
  */
  KlaystagramContract.methods.transferOwnership(tokenId, to).send({
    // 2. Set options
    from: getWallet().address,
    gas: '20000000',
  })
    .once('transactionHash', (txHash) => {
      // 3. Show transaction progress in transaction life cycle
      ui.showToast({
        status: 'pending',
        message: `Sending a transaction... (transferOwnership)`,
        txHash,
      })
    })
    .once('receipt', (receipt) => {
      ui.showToast({
        status: receipt.status ? 'success' : 'fail',
        message: `Received receipt! It means your transaction is
          in klaytn block (#${receipt.blockNumber}) (transferOwnership)`,
        txHash: receipt.transactionHash,
      })

      /**
       * 4. If transaction successfully gets into block,
       * call updateOwnerAddress function to update feed 
      */
      dispatch(updateOwnerAddress(tokenId, to))
    })
    .once('error', (error) => {
      ui.showToast({
        status: 'error',
        message: error.toString(),
      })
    })
}
```



### 4) Update information in redux store: `updateOwnerAddress` action

After transfering ownership, FeedPhoto needs to be rerendered with new owner's address.  
To update new owner's address, let's call `feed` data from store and find the photo that has the tokenId from the receipt. Then push new owner's address to photo's `OWNER_HISTORY` and setFeed.


```js
const updateOwnerAddress = (tokenId, to) => (dispatch, getState) => {
  const { photos: { feed } } = getState()
  const newFeed = feed.map((photo) => {
    if (photo[ID] !== tokenId) return photo
    photo[OWNER_HISTORY].push(to)
    return photo
  })
  dispatch(setFeed(newFeed))
}
```


[Next: Run app](9-run-app.md)