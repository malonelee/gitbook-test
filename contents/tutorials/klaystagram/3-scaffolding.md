---
title: C. Scaffolding
sidebar: mydoc_sidebar_tutorial
permalink: klaystagram/3-scaffolding.html
folder: mydoc
---

## C. Scaffolding installation
![yo-klaystagram-install.gif](../../../images/klaystagram-scaffolding.png)

### 1) Install Yeoman Generator  
Yeoman Generator is generic scaffolding tool for creating any kind of app.  
We will use it to create a klaytn-based blockchain application.  
\- `$ npm install -g yo`

### 2) Install Klay-Dapp Generator  
\- `$ npm install -g generator-klay-dapp`

### 3) Scaffold it!  
\- `$ mkdir klaystagram` - Make a new directory called 'klaystagram'  
\- `$ cd klaystagram` - Enter the directory  
\- `$ yo klaystagram` - Scaffold klay-dapp  

Now your 'Klaystagram' directory should be filled with files.
Let's look at them in detail.

#### \*Do you want to run Klaystagram app right now?
Actually the package you just downloaded is ready to go without any modification. If you want to run the app right away and see how it works, type in `$ npm intall` and `$ npm run local`. Application will pop up right away.

[Next: Directory structure](4-directory-structure.md)
