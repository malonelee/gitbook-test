---
title: H-2. Feed component
sidebar: mydoc_sidebar_tutorial
permalink: klaystagram/8-2-feedpage-feed.html
folder: mydoc
---


## H-2. Feed component

![klaystagram-feed](../../../images/klaystagram-feed.png)

1. `Feed` component's role
2. Read data from contract: `getFeed` method  
3. Save data to store: `setFeed` action  
4. Show data in component: `Feed` component

### 1) `Feed` component's role  
In chapter [E. Write Klaystagram smart contract](@TODO),  we wrote `PhotoData` struct, and located it inside `_photoList` mapping. Feed component's role is as follows:
1. Read `PhotoData` via calling Klaystagram contract method (`redux/actions/photos.js`)
2. Show `PhotoData`(feed) with its owner information (`components/Feed.js`)

### 2) Read data from contract: `getPhoto` method  
1. Call contract method: `getTotalPhotoCount()`  
If there 0 photo, call `setFeed` action with empty array.
2. Call contract method:`getPhoto(id)`  
If there is photo data, call all of photo data as promises and push it in the feed array. When all promises have resolved, return feed array. 
3. Call redux action: `setFeed(feed)`  
Get resolved feed array and save it to redux store.
```js
// src/redux/photos.js

const setFeed = (feed) => ({
  type: SET_FEED,
  payload: { feed },
})

export const getFeed = () => (dispatch) => {
  // 1. Call contract method(READ): `getTotalPhotoCount()`
  // If there is no photo data, call `setFeed` action with empty array
  KlaystagramContract.methods.getTotalPhotoCount().call()
    .then((res) => {
      if (res === 0) return dispatch(setFeed([]))
      let total = res
      const feed = []
      while (total) {
        // 2. Call contract method(READ):`getPhoto(id)`
        // If there is photo data, call all of them
        feed.push(KlaystagramContract.methods.getPhoto(total).call())
        total--
      }
      return Promise.all(feed)
    })
    .then((feed) => {
      // 3. Call actions: `setFeed(feed)`
      // Save photo data(feed) to store
      dispatch(setFeed(feed))
    })
}
```

### 3) Save data to store: `setFeed` action
After we successfully fetch photo data(feed) from Klaystagram contract, we call `setFeed(feed)` action. This action takes payloads of information from contract and save it in redux store.

### 4) Show data in component: `Feed` component
```js
// src/components/Feed.js
import React, { Component } from 'react'
import { connect } from 'react-redux'
import moment from 'moment'
import Loading from 'components/Loading'
import PhotoHeader from 'components/PhotoHeader'
import PhotoInfo from 'components/PhotoInfo'
import CopyrightInfo from 'components/CopyrightInfo'
import TransferOwnershipButton from 'components/TransferOwnershipButton'
import { drawBytesToImage, last } from 'utils/misc'

import * as photoActions from 'redux/actions/photos'

import './Feed.scss'

class Feed extends Component {
  constructor(props) {
    super(props)
    this.state = {
      isLoading: !props.feed,
    }
  }

  static getDerivedStateFromProps = (nextProps, prevState) => {
    const isUpdatedFeed = (nextProps.feed !== prevState.feed) && (nextProps.feed !== null)
    if (isUpdatedFeed) {
      return { isLoading: false }
    }
    return null
  }

  componentDidMount() {
    const { feed, getFeed } = this.props
    if (!feed) getFeed()
  }

  finishLoading = () => this.setState({ isLoading: false })

  render() {
    const { feed, userAddress } = this.props

    if (this.state.isLoading) return <Loading />

    return (
      <div className="Feed">
        {feed.length !== 0
          ? feed.map((photo) => {
            const {
              0: id,
              1: ownerHistory,
              2: data,
              3: name,
              4: location,
              5: caption,
              6: timestamp,
            } = photo
            const originalOwner = ownerHistory && ownerHistory[id]
            const currentOwner = ownerHistory && last(ownerHistory)
            const imageUrl = drawBytesToImage(data)
            const issueDate = moment(timestamp).fromNow()
            return (
              <div className="FeedPhoto" key={id}>
                <PhotoHeader
                  currentOwner={currentOwner}
                  location={location}
                />
                <div className="FeedPhoto__image">
                  <img src={imageUrl} alt={name} />
                </div>
                <div className="FeedPhoto__info">
                  <PhotoInfo
                    name={name}
                    issueDate={issueDate}
                    caption={caption}
                  />
                  <CopyrightInfo
                    className="FeedPhoto__copyrightInfo"
                    id={id}
                    issueDate={issueDate}
                    originalOwner={originalOwner}
                    currentOwner={currentOwner}
                  />
                  {
                    userAddress === currentOwner && (
                      <TransferOwnershipButton
                        className="FeedPhoto__transferOwnership"
                        id={id}
                        issueDate={issueDate}
                        currentOwner={currentOwner}
                      />
                    )
                  }
                </div>
              </div>
            )
          })
          : <span className="Feed__empty">No Photo :D</span>
        }
      </div>
    )
  }
}

const mapStateToProps = (state) => ({
  feed: state.photos.feed,
  userAddress: state.auth.address,
})


const mapDispatchToProps = (dispatch) => ({
  getFeed: () => dispatch(photoActions.getFeed()),
})

export default connect(mapStateToProps, mapDispatchToProps)(Feed)
```

At the first time, you can only see the text "No photo :D" because there is no photo data in contract yet.  
Let's make a UploadPhoto component to send photo data to contract! 

[Next: F-3. UploadPhoto component](@TODO)  


[Next: Feed component](8-3-feedpage-uploadphoto.md)