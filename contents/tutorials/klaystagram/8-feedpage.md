---
title: H. Feedpage
sidebar: mydoc_sidebar_tutorial
permalink: klaystagram/8-feedpage.html
folder: mydoc
---


## H. FeedPage
![FeedPage](../../../images/klaystagram-feedpage.png)

FeedPage is consisted of 3 main components that interact with `Klaystagram` contract.  

F-2. `Feed` component  
F-3. `UploadPhoto` component  
F-4. `TransferOwnership` component

```js
// src/pages/FeedPage.js

const FeedPage = () => (
  <main className="FeedPage">
    <UploadButton />               // F-3. UploadPhoto
    <Feed />                       // F-2. Feed
  </main>
)
```

```js
// src/components/Feed.js

<div className="Feed">
  {feed.length !== 0
    ? feed.map((photo) => {
      // ...
      return (
        <div className="FeedPhoto" key={id}>
        
            // ...
            {
              userAddress === currentOwner && (
                <TransferOwnershipButton   // F-4. TransferOwnership
                  className="FeedPhoto__transferOwnership"
                  id={id}
                  issueDate={issueDate}
                  currentOwner={currentOwner}
                />
              )
            }
            // ...
        </div>
      )
    })
    : <span className="Feed__empty">No Photo :D</span>
  }
</div>
)
```
To make component interact with contract, there are 3 steps.

**First**, create `KlaystagramContract` instance to connect contract with front-end. 
**Second**, using `KlaystagramContract` instance, make API functions that interact with contract in `redux/actions`  
**Third**, call functions in each component (`src/components/..`)

Let's build it!


[Next: Connect contract](8-1-feedpage-connect-contract.md)