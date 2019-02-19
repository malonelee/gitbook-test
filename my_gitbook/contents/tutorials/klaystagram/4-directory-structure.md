---
title: D. Directory structure
sidebar: mydoc_sidebar_tutorial
permalink: klaystagram/4-directory-structure.html
folder: mydoc
---

## D. Directory structure
```
|-- contracts
|-- migrations
|-- truffle.js
|-- static
|-- src
    |-- klaytn
      |-- caver.js
      |-- Klaystagram.js
    |-- redux
    |-- pages
      |-- AuthPage.js
      |-- MainPage.js
    |-- components
      |-- UploadPhoto.js
      |-- Feed.js
      |-- TransferOwnership.js
      |-- ...
    |-- styles
    |-- utils
    |-- index.js
    |-- App.js
```

`contracts/`: Contains Solidity contract files.  

`migrations/`: Contains javascript files that handle smart contract deployments.

`truffle.js`: Truffle configuration file.  

`src/klaytn`: Contains files that help interact with klaytn blockchain.
* `src/klaytn/caver.js`: Instantiates caver within configured setting.  
cf) caver-js is a RPC call library which makes a connection to klaytn node, interacting with node or smart contract deployed on klaytn.
* `src/klaytn/Klaystagram.js`: Creates an instance of contract using caver-js API. You can interact with contract through the instance  

`src/redux`: Creates API functions that interacts with contract and keep tracks of consequent data.

`src/pages`: Contains frontend javascript files. 
* `src/pages/AuthPage.js`: Contains sign up and login form. You can generate private key in the sign up form and use it to login on the app.
* `src/pages/FeedPage.js`: Reads photo data from the contract and show them to users. Also users can upload their pictures.


`src/components`: Contains frontend component files. =  
* `src/components/Feed.js`: Reads data from contract and displays photos
* `src/components/UploadPhoto.js`: Uploads photo by sending transaction to contract 
* `src/components/TransferOwnership.js`: Transfers photo's ownership by sending transaction


`src/styles`: Overall style definition regarding stylesheet  
`static/`: Contains static files, such as images.
`src/index.js`: App's index file. ReactDOM.render logic is in here.  
`src/App.js`: App's root component file.  

[Next: Write smart contract](5-write-smart-contract.md)