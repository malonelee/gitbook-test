---
title: A. Introduction
sidebar: mydoc_sidebar_tutorial
permalink: klaystagram/1-introduction.html
folder: mydoc
---
# Klaytn-Based NFT Photo Licensing Application Tutorial

## Table of Contents
* [A. Introduction](1-introduction.md)
* [B. Environment Setup](2-environment-setup.md)
* [C. Scaffolding](3-scaffolding.md)
* [D. Directory Structure](4-directory-structure.md)
* [E. Write Klaystagram Smart Contract](5-write-smart-contract.md)
* [F. Deploy Contract](6-deploy-contract.md)
* [G. Frontend Code Overview](7-frontend-overview.md)
* [H. FeedPage](8-feedpage.md)
  - [H-1. Connect Contract to Frontend](8-1-feedpage-connect-contract.md)
  - [H-2. UploadPhoto Component](8-2-feedpage-uploadphoto.md)
  - [H-3. Feed Component](8-3-feedpage-feed.md)
  - [H-4. TransferOwnership Component](8-4-feedpage-transferownership.md)
* [I. Run App](9-run-app.md)

## A. Introduction

<video src="../../../images/klaystagramo-video.mov" autoplay poster="../../../images/klaystagram-sending-trasaction.png" style="width: 700px;"></video>

In this tutorial, we are going to make "Klaytn-based NFT photo licensing application", also known as `Klaystagram`.

NFT refers to non-fungible token, which is a special type of token that represents a unique asset. As the name non-fungible implies, every single token is unique. And this uniquenss of NFT opens up new horizons of asset digitization. For example it can be used to represent digital art, game items, or any kind of unique assets and allow people to trade them. For more information, refer to this [article](https://coincentral.com/nfts-non-fungible-tokens/).  

In `Klaystagram`, every token represents users' unique pictures. When a user uploads a photo, a unique token is created containing its ownership information and image source. All transactions are recorded on blockchain, so even service provider does not have control over the uploaded photos. Considering the purpose of this tutorial, only core functions are implemented. After finishing this tutorial, try adding some more cool features and make your own creative service.

There are three main features.

1. **Upload photo**  
Users can upload photo along with description on the Klaytn blockchain. The photo will be tokenized.

2. **Feed**  
Shows all the photos uploaded on the blockchain.

3. **Transfer ownership**  
The owner of the photo can transfer photo ownership to another user and it will be shown in ownership history.

We will learn how to make an web application that interacts with smart contracts. Thus, basic knowledge regarding Solidity, Javascript, and React is required.  

[Next: Environment setup](2-environment-setup.md)
