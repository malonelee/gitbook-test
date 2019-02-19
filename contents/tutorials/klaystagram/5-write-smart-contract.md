---
title: E. Write Smart Contract
sidebar: mydoc_sidebar_tutorial
permalink: klaystagram/5-write-smart-contract.html
folder: mydoc
---

## E. Write Smart Contract

1) Background
2) Contract setup
3) Set events and data structure
4) Write functions
    * `uploadPhoto`
    * `transferOwnership`

### 1) Background
We will make a simple contract called "Klaystagram".  
* `PhotoData` struct is defined to store various photo data.  
* User can upload photo and transfer the ownership photo via `uploadPhoto` and `transferOwnership` functions.

### 2) Contract setup 
* Specify solidity version. We recommend using 0.4.24 stable version.
* We will make use of ERC721 standard to build non-fungible tokens.  
  * Import `ERC721.sol` and `ERC721Enumerable.sol`
  * Check out detailed information about ERC721 at [erc721.org](http://erc721.org)
```solidity
pragma solidity 0.4.24;

import "./ERC721/ERC721.sol";
import "./ERC721/ERC721Enumerable.sol";

contract Klaystagram is ERC721, ERC721Enumerable {
```
### 3) Set events and data structure
We need to set up an event to keep track of activities on blockchain.  

As for data structure, mapping `_photoList` takes a uint256 `tokenId` to map a specific `PhotoData` struct.  
```solidity
event PhotoUploaded (uint indexed tokenId, bytes photo, string title, string location, string description);

mapping (uint256 => PhotoData) private _photoList;

struct PhotoData {
    uint256 tokenId;                       // Unique token id
    address[] ownerHistory;                // History of all previous owners
    bytes photo;                           // Image source
    string title;                          // Title of photo
    string location;                       // Location where photo is taken
    string description;                    // Short description about the photo
    uint256 timestamp;                     // Uploaded time
}
```
### 4) Write functions

Let's write some functions that interact with the contract. In this tutorial let us only consider two functions: `uploadPhoto` and `transferOwnership`. Check out `Klaystagram.sol` to see the whole set of functions.

#### `uploadPhoto`
`uploadPhoto` function takes 4 arguments including photo's image source. To keep things simple, `tokenId` will start from 0 and will increase by 1.  

`_mint` function is from ERC721 contract. It creates a new token and assign it to a specific address, which in this case, `msg.sender`.  

Finally, initialize `PhotoData` struct, locate it inside `_photoList` mapping, and push the owner address into `ownerHistory` array. And don't forget to emit the event we just created.
```solidity
function uploadPhoto(uint8[] photo, string title, string location, string description) public {
    uint256 tokenId = totalSupply() + 1;

    _mint(msg.sender, tokenId);

    address[] memory ownerHistory;

    PhotoData memory newPhotoData = PhotoData({
        tokenId : tokenId,
        ownerHistory : ownerHistory,
        photo : photo,
        title: title,
        location : location,
        description : description,
        timestamp : now
    });

    _photoList[tokenId] = newPhotoData;
    _photoList[tokenId].ownerHistory.push(msg.sender);

    emit PhotoUploaded(tokenId, photo, title, location, description, now);
}
```

#### `transferOwnership`
Let's take a look at `transferOwnership` function. When transfering photo ownership, we need to do two things. First, we have to reassign the owner, and then we have to push new owner address into `ownerHistory` array. 

To do this, `transferOwnership` first calls `safeTransferFrom` function from ERC721 standard, which eventually calls `transferFrom` function. As mentioned above, right after token transfer is successfully done, we have to push new owner information into `ownerHistory` array, and that is exactly why `transferFrom` is overridden as below.
```solidity
/**
  * @notice safeTransferFrom function checks whether receiver is able to handle ERC721 tokens,
  *  thus less possibility of tokens being lost. After checking is done, it will call transferFrom function defined below
  */
function transferOwnership(uint256 tokenId, address to) public returns(uint, address, address, address) {
    safeTransferFrom(msg.sender, to, tokenId);
    uint ownerHistoryLength = _photoList[tokenId].ownerHistory.length;
    return (
        _photoList[tokenId].tokenId, 
        //original owner
        _photoList[tokenId].ownerHistory[0],
        //previous owner, length cannot be less than 2
        _photoList[tokenId].ownerHistory[ownerHistoryLength-2],
        //current owner
        _photoList[tokenId].ownerHistory[ownerHistoryLength-1]);
}

/**
  * @notice Recommand using transferOwnership instead of this function
  * @dev Overrode transferFrom function to make sure that every time ownership transfers
  *  new owner address gets pushed into ownerHistory array
  */
function transferFrom(address from, address to, uint256 tokenId) public {
    super.transferFrom(from, to, tokenId);
    _photoList[tokenId].ownerHistory.push(to);
}
```

[Next: Deploy contract](6-deploy-contract.md)
