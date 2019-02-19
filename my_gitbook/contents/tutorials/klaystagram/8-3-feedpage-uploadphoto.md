---
title: H-3. UploadPhoto component
sidebar: mydoc_sidebar_tutorial
permalink: klaystagram/8-3-feedpage-uploadphoto.html
folder: mydoc
---


## H-2. UploadPhoto component
![upload photo](../../../images/klaystagram-uploadphoto.png)

1. `UploadPhoto` component's role
2. Component code
3. Interact with contract  
4. Update data to store: `updateFeed` function

### 1) `UploadPhoto` component's role
Through `UploadPhoto` component, we can upload photos to the Klaytn blockchain in the form of NFT(non-fungible-token). To do this, it works as follows:
1. Send transaction by calling `uploadPhoto`.
2. After sending transaction, show progress with `Toast` component in trasaction life cycle.
3. When transaction gets into block, update new `PhotoData` to store.


### 2) Component code  
```js
// src/components/UploadPhoto.js

import React, { Component } from 'react'
import { connect } from 'react-redux'
import ui from 'utils/ui'
import Input from 'components/Input'
import InputFile from 'components/InputFile'
import Button from 'components/Button'

import * as photoActions from 'redux/actions/photos'

import './UploadPhoto.scss'

class UploadPhoto extends Component {
  state = {
    file: '',
    fileName: '',
    location: '',
    caption: '',
  }

  handleInputChange = (e) => {
    this.setState({
      [e.target.name]: e.target.value,
    })
  }

  handleFileChange = (e) => {
    this.setState({
      file: e.target.files[0],
      fileName: e.target.files[0].name,
    })
  }

  handleSubmit = (e) => {
    e.preventDefault()
    const { file, fileName, location, caption } = this.state

    /** Write data to contract (Sending transction) **/
    this.props.uploadPhoto(file, fileName, location, caption)
    ui.hideModal()
  }

  render() {
    const { fileName, location, caption } = this.state
    return (
      <form className="UploadPhoto" onSubmit={this.handleSubmit}>
        <InputFile
          className="UploadPhoto__file"
          name="file"
          type="file"
          fileName={fileName}
          accept="image/png, image/jpeg"
          onChange={this.handleFileChange}
          required
        />
        <div className="UploadPhoto__content">
          <Input
            className="UploadPhoto__location"
            name="location"
            value={location}
            onChange={this.handleInputChange}
            placeholder="Where did you take this photo?"
            required
          />
          <textarea
            className="UploadPhoto__caption"
            name="caption"
            value={caption}
            onChange={this.handleInputChange}
            placeholder="Upload your memories"
            required
          />
        </div>
        <Button
          className="UploadPhoto__upload"
          type="submit"
          title="Upload"
        />
      </form>
    )
  }
}

const mapDispatchToProps = (dispatch) => ({
  uploadPhoto: (file, fileName, location, caption) =>
    dispatch(photoActions.uploadPhoto(file, fileName, location, caption)),
})

export default connect(null, mapDispatchToProps)(UploadPhoto)
```

### 3) Interact with contract
```js
// src/redux/actions/photo.js

export const uploadPhoto = (
  file,
  fileName,
  location,
  caption
) => (dispatch, getState) => {

  // 1. Read file(image)'s data as an ArrayBuffer
  const reader = new window.FileReader()
  reader.readAsArrayBuffer(file)
  reader.onloadend = () => {

    // 2. Convert ArrayBuffer to bytes
    const buffer = Buffer.from(reader.result)
    const bytesString = "0x" + buffer.toString('hex')

    /**
     * 3. Call contract method(SEND): uploadPhoto
     * Send transaction with bytes of photo image and descriptions
    */
    KlaystagramContract.methods.uploadPhoto(buffer, fileName, location, caption).send({
      from: getWallet().address,
      gas: '20000000',
    })
      .once('transactionHash', (txHash) => {
        // 4. After sending transaction,
        // Show progress with `Toast` component in trasaction life cycle
        ui.showToast({
          status: 'pending',
          message: `Sending a transaction... (uploadPhoto)`,
          txHash,
        })
      })
      .once('receipt', (receipt) => {
        ui.showToast({
          status: receipt.status ? 'success' : 'fail',
          message: `Received receipt! It means your transaction is
          in klaytn block (#${receipt.blockNumber}) (uploadPhoto)`,
          txHash: receipt.transactionHash,
        })receipt)

        /**
         * 5. If transaction successfully gets into block,
         * Call updateFeed function to add new photo data
        */
        const tokenId = receipt.events.PhotoUploaded.returnValues[0]
        dispatch(updateFeed(tokenId))
      })
      .once('error', (error) => {
        ui.showToast({
          status: 'error',
          message: error.toString(),
        })
      })
  }
}
```

#### Send transaction to contract: `uploadPhoto` method
We created Contract instance above. Let's make function to write photo data on Klaytn.  
Although reading data is free, writing data pays cost for computation & data storage. This cost is called `'gas'` and this process is called `'Sending a transaction'`.

For these reasons, sending a transaction needs two property `from` and `gas`.

1. Read file's data from `UploadPhoto` component as an ArrayBuffer using `FileReader`.
2. Convert ArrayBuffer to bytes (ArrayBuffer -> hex -> bytes).
    * Prefix `0x` - to satisfy bytes format
3. Call contract method (SEND): `uploadPhoto` .
    * `from:` account that sends this transaction (= who will pay for this transaction)  
    * `gas:` amount of maximum cost willing to pay for sending the transaction
4. After sending transaction, show progress with `Toast` component in trasaction life cycle.
5. If transaction success to get into block, Call updateFeed function to add new photo data to feed in store.

>[Toast (pending, success, error) gif](@TODO)

**cf) Transaction life cycle:**  
After sending transaction, you can get transaction life cycle (`transactionHash`, `receipt`, `error`).  
- In `transactionHash` cycle, you can get transaction hash before sending actual transaction.  
- In `receipt` cycle, you can get transaction receipt. It means your transaction got into the block. You can check the block number by `receipt.blockNumber`.  
- `error` cycle is triggered when something goes wrong.



### 4. Update data to store: `updateFeed` function
After successfully sending transaction to contract, FeedPage needs to be updated.  
In order to update feed we need to get the new photo data we've just uploaded. Let's call `getPhoto()` with tokenId in receipt that comes right after sending transaction. Then add a new photo data to feed in store.  

```js
// src/redux/actions/photo.js

/**
 * 1. Call contract method (CALL): getPhoto()
 * To get new photo data we've just uploaded,
 * call `getPhoto()` with tokenId from receipt after sending transaction
*/
const updateFeed = (tokenId) => (dispatch, getState) => {
  KlaystagramContract.methods.getPhoto(tokenId).call()
    .then((newPhoto) => {
      const { photos: { feed } } = getState()
      const newFeed = [newPhoto, ...feed]
      
      // 2. update new feed to store
      dispatch(setFeed(newFeed))
    })
}
```

[Next: TransferOwnership component](8-4-feedpage-transferownership.md)