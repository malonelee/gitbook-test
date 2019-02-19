---
title: Managing Accounts
sidebar: mydoc_sidebar_gs
permalink: getting_started/managing_accounts.html
folder: mydoc
---
## Managing Accounts
**`WARNING`**:
Remember your password.
If you lose the password of your account, you will not be able to access that account.


**There is no _forgot my password_ option here.
Never forget it.**


The Klaytn platform binary `klay` provides account management via the `account` command:

```
$ klay account <command> [options...] [arguments...]
```

The directory where keystore file is generated could be specified as belows,
```
$ klay account new --datadir "./data/dd"
```
If the directory is not specified, keystore file would be generated as belows,  

Mac: ~/Library/Klaytn  
Linux: ~/.klay

The command `account` lets you create new accounts, lists all existing accounts, imports a private
key into a new account, migrates to the newest key format, and changes your password.

It supports the interactive mode, when you are prompted for password as well as
the non-interactive mode where passwords are supplied via a given password file.
The non-interactive mode is only meant for scripted use on test networks or known safe
environments.

Make sure you remember the password you gave when creating a new account (with new, update
or import). Without it, you are not able to unlock your account.

Note that exporting your key in an unencrypted format is NOT supported.

Keys are stored under `<DATADIR>/keystore`. Make sure backup your keys regularly!
<!-- "See [DATADIR backup & restore](https://github.com/ethereum/go-ethereum/wiki/Backup-&-restore) for more information." If a custom datadir and keystore option are given the keystore option takes preference over the datadir option. -->

The newest format of the keyfiles is: `UTC--<created_at UTC ISO8601>-<address hex>`. The
order of accounts when listing is lexicographic, but accounts are listed in the order of creation
due to the timestamp.

It is safe to transfer the entire directory or the individual keys therein between
Klaytn nodes. Note that in case you are adding keys to your node from a different node,
the order of accounts may change. So make sure you do not rely on the index in your
scripts or code snippets.

And again. **DO NOT FORGET YOUR PASSWORD**.

```
COMMANDS:
     list    Print summary of existing accounts
     new     Create a new account
     update  Update an existing account
     import  Import a private key into a new account
```

You can get info about subcommands by `klay account <command> --help`.
```shell
$ klay account list --help
NAME:
   klay account list - Print summary of existing accounts

USAGE:
   klay account list [command options] [arguments...]

DESCRIPTION:

Print a short summary of all accounts

OPTIONS:
   --dbtype value                  Blockchain storage database type ("leveldb", "badger") (default: "leveldb")
   --datadir "/home/ubuntu/.klay"  Data directory for the databases and keystore
   --keystore                      Directory for the keystore (default = inside the datadir)
```

[//]: # "Accounts can also be managed via the [Javascript Console]#(https://github.com/ethereum/go-ethereum/wiki/JavaScript-Console)"

### Example of Interactive Use

#### Creating an Account

```shell
$ klay account new
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {308c9353883d9a951e03fb70c510c27d8f9ed836}
```

#### Listing Accounts in a Custom `keystore` Directory

```shell
$ klay account list --keystore /your/keystore/dir 
Account #0: {fa8ac1860fcb5f6dc8eecadfb3dd1eac3e52a495} keystore:///Klaytn/keystore/UTC--2018-09-14T06-01-30.089670144Z--fa8ac1860fcb5f6dc8eecadfb3dd1eac3e52a495
Account #1: {ac7f70a2b1237407c3edeece79a22cd06398de2f} keystore:///Klaytn/keystore/UTC--2018-09-14T06-02-40.251750183Z--ac7f70a2b1237407c3edeece79a22cd06398de2f
```

#### Importing a Private Key with a Custom `datadir`

```shell
$ klay account import --datadir /someOtherKlayDataDir ./privatekey.txt
The new account will be encrypted with a passphrase.
Please enter a passphrase now.
Passphrase:
Repeat Passphrase:
Address: {c2d7cf95645d33006175b78989035c7c9061d3f9}
```

`privatekey.txt` should have a private key in a plain text. For example, you must have gotten a private key like this when you made an account from the Klaytn wallet.

0x60d8a1d4db5dfa0a05167e2e1e4c34fe15900b62291ac8335e36a8e86a3f171f

delete **0x** at the very first part of your private key. And then you are ready to use it.

```
$ echo "60d8a1d4db5dfa0a05167e2e1e4c34fe15900b62291ac8335e36a8e86a3f171f" > privatekey.txt
$ cat privatekey.txt
60d8a1d4db5dfa0a05167e2e1e4c34fe15900b62291ac8335e36a8e86a3f171f
```

### Example of Non-Interactive Use

You supply a plaintext password file as argument to the `--password` flag. The
file consists of the raw characters of the password, followed by a single newline.


**NOTE**: Supplying the password directly as part of the command line is not recommended,
but you can always use shell trickery to get around this restriction.

```
$ klay account new --password /path/to/password

$ klay account import  --datadir /someOtherKlayDataDir --password /path/to/anotherpassword ./key.prv
```

## Creating Accounts

### Creating a New Account

```shell
$ klay account new
$ klay account new --password /path/to/passwdfile
$ klay account new --password <(echo $mypassword)
```

These commands create a new account and print the address.

On the console, you can call the following function:

```javascript
> personal.newAccount("passphrase")
```

The account is saved in an encrypted format. You **must** remember this passphrase to unlock
your account in the future.

For non-interactive use the passphrase can be specified with the `--password` flag:

```shell
$ klay account new --password <passwordfile>
```

Note that this is meant to be used for testing only, it is a bad idea to save your
password to a file or expose it in any other way.

### Importing an Account

```shell
$ klay account import <keyfile>
```

This imports an unencrypted private key from `<keyfile>`, creates a new account,
and prints the address.

The keyfile is assumed to contain an unencrypted private key as canonical EC raw bytes
encoded into hex.

The account is saved in the encrypted format, you are prompted for a passphrase.

You must remember this passphrase to unlock your account in the future.

For non-interactive use, the passphrase can be specified with the `--password` flag:

```shell
klay account import --password <passwordfile> <keyfile>
```

**NOTE**: Since you can directly copy your encrypted accounts to another Klaytn
instance, this import/export mechanism is not needed when you transfer an account between
nodes.

**`WARNING`**: When you copy private keys into an existing node's keystore, the order of accounts
may change. Therefore, you make sure either do not rely on the account
order or doublecheck and update the indexes used in your scripts.

**`WARNING`**: If you use the password flag with a password file, best to make sure the file
is not readable or even listable for anyone but you. You achieve this with:

```shell
touch /path/to/password
chmod 700 /path/to/password
cat > /path/to/password
>I type my pass here^D
```

## Listing Your Accounts

From the command line, call the CLI with:

```shell
$ klay account list
Account #0: {5afdd78bdacb56ab1dad28741ea2a0e47fe41331} keystore:///tmp/mykeystore/UTC--2017-04-28T08-46-27.437847599Z--5afdd78bdacb56ab1dad28741ea2a0e47fe41331
Account #1: {9acb9ff906641a434803efb474c96a837756287f} keystore:///tmp/mykeystore/UTC--2017-04-28T08-46-52.180688336Z--9acb9ff906641a434803efb474c96a837756287f
```

**NOTE**:
This order can change if you copy keyfiles from other nodes, so make sure you either do not rely on indexes or make sure if you copy keys you check and update your account indexes in your scripts.

When using the console:
```javascript
> klay.accounts
["0x5afdd78bdacb56ab1dad28741ea2a0e47fe41331", "0x9acb9ff906641a434803efb474c96a837756287f"]
```

or via RPC:
```shell
# Request
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"klay_accounts","params":[],"id":1}' http://localhost:8551

# Result
{
   "jsonrpc":"2.0",
   "id":1,
   "result":[
      "0x2674ac20715302c916ad504eac53d28c5b3d14c1",
      "0x75a59b94889a05c03c66c3c84e9d2f8308ca4abd"
   ]
}
```

If you want to use an account non-interactively, you need to unlock it. You can do this on
the command line with the `--unlock` option which takes a comma-separated list of accounts
(in hex or index) as an argument so you can unlock the accounts programmatically for one
session. This is useful if you want to use your account from DApps via RPC. `--unlock`
will unlock the first account. This is useful when you created your account
programmatically, you do not need to know the actual account to unlock it.

Create an account and start a node with the account unlocked:
```shell
$ klay account new --password <(echo this is not secret)
$ klay --password <(echo "this is not secret") --unlock primary --rpccorsdomain localhost --verbosity 6 2>> log.log
```

Instead of the account address, you can use integer indexes which refers to the address
position in the account listing (and corresponds to the order of creation).

The command line allows you to unlock multiple accounts. In this case, the argument to
unlock is a comma-separated list of account addresses or indexes.

```shell
$ klay --unlock "0x407d73d8a49eeb85d32cf465507dd71d507100c1,0,5,e470b1a7d2c9c5c6f03bbaa8fa20db6d404a0c32"
```

If this construction is used non-interactively, your password file will need to contain
the respective passwords for the accounts in question, one per line.

On the console you can also unlock accounts (one at a time) for a duration (in seconds).

```shell
$ personal.unlockAccount(address, "password", 300)
```

Note that we do NOT recommend using the password argument here, since the console history
is logged, so you may compromise your account. You have been warned.

## Checking Balances of Your Accounts

To check your the coinbase account balance:
```javascript
> web3.fromPeb(klay.getBalance(klay.coinbase), "KLAY")
6.5
```

Print all balances with a JavaScript function:
```javascript
function checkAllBalances() {
    var totalBal = 0;
    for (var acctNum in klay.accounts) {
        var acct = klay.accounts[acctNum];
        var acctBal = web3.fromPeb(klay.getBalance(acct), "KLAY");
        totalBal += parseFloat(acctBal);
        console.log("klay.accounts[" + acctNum + "]: \t" + acct + " \tbalance: " + acctBal + " KLAY");
    }
    console.log("Total balance: " + totalBal + " KLAY");
};
```
That can then be executed with:
```javascript
> checkAllBalances();
  klay.accounts[0]: 0xd1ade25ccd3d550a7eb532ac759cac7be09c2719 	balance: 63.11848 KLAY
  klay.accounts[1]: 0xda65665fc30803cb1fb7e6d86691e20b1826dee0 	balance: 0 KLAY
  klay.accounts[2]: 0xe470b1a7d2c9c5c6f03bbaa8fa20db6d404a0c32 	balance: 1 KLAY
  klay.accounts[3]: 0xf4dd5c3794f1fd0cdc0327a83aa472609c806e99 	balance: 6 KLAY
```

Since this function will disappear after restarting `klay`, it can be helpful to store
commonly used functions to be recalled later. The function makes this very easy.
<!-- "[loadScript](https://github.com/ethereum/go-ethereum/wiki/JavaScript-Console#loadscript)" -->


First, save the `checkAllBalances()` function definition to a file on your computer. For
example, `/Users/username/klayload.js`. Then load the file from the interactive console:

```javascript
> loadScript("/Users/username/klayload.js")
true
```

The file will modify your JavaScript environment as if you have typed the commands
manually. Feel free to experiment!

