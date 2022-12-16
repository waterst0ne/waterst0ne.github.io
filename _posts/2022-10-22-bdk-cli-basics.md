---
title: "Bitcoin Dev Kit bdk-cli-basics00"
date: 2022-12-09T00:00:00+00:00
author: Peter
layout: post
permalink: /bdk-cli-basics/
categories: bdk
tags: [bdk, bdk-cli,tutorial]
---

## How to use bdk-cli basics
### Tutorial Goals

- My goal for this tutorial is to introduce you to [bdk-cli](https://github.com/bitcoindevkit/bdk-cli), a powerful command-line program used for Bitcoin wallet testing. You will be exposed to many of the basic techincal skills that go into creating and managing Bitcoin wallets. 
- If you've read most of the ["Mastering Bitcoin"](https://github.com/bitcoinbook/bitcoinbook) book, this tutorial could serve as a stepping stone into your Bitcoin wallet development journey.

- This short tutorial will expose you to the [bdk library ](https://docs.rs/bdk/latest/bdk/) and the practical knowledge needed for Bitcoin wallet development.


- You can create a custom modular wallet by using the [Language Bindings](https://github.com/bitcoindevkit/bdk-ffi) for **Kotlin/Java, Swift, Python** which enable the use of BDK's **Rust** library as an API. You can later use these similar steps to create your own Bitcoin mobile, desktop or even WebApp.
***

## A few things before you begin:
> Run this command to clear cache.
> This command is not needed if it's your first time of using the `bdk-cli.`
> This command clears the cache files located in the bdk-cli folder. `rm -rf ~/.bdk-cli/`

- Three things to look out for in each step of the tutorial:
    - 1) Commands for the Terminal or Shell start with the `$` symbol
    - 2) Preview of the command output. Note, not all commands will output code. 
    - 3) Preview Video of the tutorial for reference of what things should look like in action. 

***    
### Outline of Tutorial and Installation notes:
    
### Brief Outline of Tutorial
-  Step 1: Creating a mnemonic word list + XPRV (Extended Private Key)
-  Step 2: Generate testnet Receive Address
-  Step 3: Send funds to newly generated address
-  Step 4: Sync Wallet
-  Step 5: Check Balance of Wallet
-  Step 6: Create a Transaction (PSBT)
-  Step 7: Sign the Transaction (PSBT)
-  Step 8: Broadcast Transaction

### To follow this tutorial you will need:

-  **Linux** or **macOS** device.
-  If on **Windows** instructions will slightly differ.

***
### Rust and Cargo installation:```
```
$ curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

***
### `bdk-cli` installation:

```
$  cargo install --features electrum  --version 0.6.0 bdk-cli
```

## Step 0: Check Version of bdk-cli


$ `bdk-cli -V`



```
# command output preview

bdk-cli 0.5.0
```

![](https://i.imgur.com/IcuyeMS.gif)


### Preview of bdk-cli help menu

$`bdk-cli --help`

```
# command output preview

USAGE:
    bdk-cli [OPTIONS] <SUBCOMMAND>

FLAGS:
    -h, --help       
            Prints help information

    -V, --version    
            Prints version information


OPTIONS:
    -d, --datadir <DATADIR>    
            Sets the wallet data directory. Default value : "~/.bdk-bitcoin

    -n, --network <NETWORK>    
            Sets the network [default: testnet]  [possible values: bitcoin, testnet, signet, regtest]


SUBCOMMANDS:
    compile    Compile a miniscript policy to an output descriptor
    help       Prints this message or the help of the given subcommand(s)
    key        Subcommands for Key operations
    repl       Options to configure a SOCKS5 proxy for a blockchain client connection
    wallet     Wallet subcommands that can be issued without a blockchain backend


```
 ---
 
## Step 1: Seed Generate

### 1a: Mnemonic word-list + XPRV (Exteneded Private Key) :key: 

  $ `bdk-cli key generate | tee key.json`

    
```
{
  "fingerprint": "42b15d2f",
  "mnemonic": "party salon worth satoshi envelope suggest garlic dry add pitch throw clap keen narrow antique oyster ketchup purchase gasp visual work venue fog crater",
  "xprv": "tprv8ZgxMBicQKsPdwpamtjqMFpYRTafnE1bN2SphLEybCtRKakk6S1TgQCsZgiBwJuJNWe3jYdgVCTsKf9weMxj6tW4zNNKWptykszJpS2L8wE"
}

```



![](https://i.imgur.com/ii62Hul.gif)

***
### 1b: Save XPRV (Extended Private Key) into environment variable
$ `export XPRV_00=$(cat key.json | jq -r .xprv)`

![](https://i.imgur.com/KYW2Cdo.gif)

***
### 1c: Verify environment variable XPRV_00 is active
$ `env | grep XPRV`

![](https://i.imgur.com/ZahbJwe.gif)

***
### 1d: Create Descriptor and Save into environment variable

 $ `export my_descriptor="wpkh($XPRV_00/84h/1h/0h/0/*)"`

![](https://i.imgur.com/UV4Vgsq.gif)

***
### 1e: Verify environment variable my_descriptor is active

$ `env | grep my_descriptor`

![](https://i.imgur.com/s7ZeRQN.gif)



***
## Step 2: Generate Receive-Address

$ `bdk-cli wallet --wallet wallet_name --descriptor $my_descriptor get_new_address` 


![](https://i.imgur.com/P8PjTAo.gif)





```
 The output below confirms the command was successful.

{
  "address": "tb1qrh4sq5va0unqtxyfv8al9lz3sna3988cj59uya"
}
```

Note: If you get the error below just delete the folder cache.

`$ rm -rf ~/./bdk_cli/`
```
### ERROR PREVIEW 

[2022-12-16T04:12:26Z ERROR bdk_cli] Descriptor checksum mismatch. Are you using a different descriptor for an already defined wallet name? (if you are not specifying the wallet name it is automatically named based on the descriptor)


```

This erases the cache files located in the bdk-cli binary folder.
Note: This happens when you name the wallet the same name as previous created wallet. 

***
## Step 3: Send testnet bitcoin to the newly created receive-address 

These Testnet faucets allow you to send funds to your newly created address.
- [Bitcoin Testnet Faucet LINK#1](https://testnet-faucet.mempool.co)
- [Bitcoin Testnet Faucet LINK#2](https://bitcoinfaucet.uo1.net)

***
## Step 4: Sync the wallet

$ `bdk-cli wallet --wallet wallet_name  --descriptor $my_descriptor sync`



```
The output below confirms the command was successful.

{}
```


![](https://i.imgur.com/WFYBgVB.gif)


***
## Step 5: Check the balance


$ `bdk-cli wallet --wallet wallet_name  --descriptor $my_descriptor get_balance `


> Note: The balance will only show after the transaction has been confirmed in a block at least once.


    
 
```
The output below confirms the command was successful:

{
  "satoshi": 100000
}

```


![](https://i.imgur.com/v8MAYB2.gif)


***
## Step 6: Create Transaction (PSBT)

 To create a PSBT (partially-signed-bitcoin-transaction) run  the command 

$ `bdk-cli wallet --wallet wallet_name --descriptor $my_descriptor create_tx --to mkHS9ne12qx9pS9VojpwU5xtRd4T7X7ZUt:50000`

![](https://i.imgur.com/EUCovcJ.gif)


```
 The output below confirms the command was successful.

{
  "details": {
    "confirmation_time": null,
    "fee": 113,
    "received": 0,
    "sent": 123000,
    "transaction": null,
    "txid": "029173d76253e3441f9dc26f91e6ef30dff486848e91a7941f0cacd0af25ee30"
  },
  "psbt": "cHNidP8BAFUBAAAAAak8uMR3UGkAGUKWsq8Mv45qg2fdD93JQRIsa2P0wFloAQAAAAD/////AQfgAQAAAAAAGXapFDRKD0jKFQ7CuQOBdmC5tosTpnAmiKwAAAAAAAEA3gIAAAAAAQFY9sVfEEbyjrHXSlxXDxL+71WOMnsPpVElwk+3E/J9vAAAAAAA/v///wIYZRIAAAAAABYAFBKYf7yF+ss6EFdw2rDZTfdLhep8eOABAAAAAAAWABQd6wBRnX8mBZiJYfvy/FGE+xKc+AJHMEQCIFSIkvEUI9yUgEw4JocRs1aiVsBlKKXrOQaQb3XFqR21AiBqiEVzCVVSRGjckyPDgAQBnOdSzBYR6Rw6KFcCP+E27wEhAwIlXdfM2WYnYa36Hp4MS6YkplBAgBsb1tYG9NiWFWTKzPYhAAEBH3jgAQAAAAAAFgAUHesAUZ1/JgWYiWH78vxRhPsSnPgiBgP80FpaWYQzGzCnNI9blXbei61YpAmtoezMRxpVvBJ6SxgTizKsVAAAgAEAAIAAAACAAAAAAAAAAAAAAA=="
}

```

***

### 6a: export PSBT to environment-variable

$ `export PSBT="PASTE_PSBT_HERE"`

![](https://i.imgur.com/CEDKcPZ.gif)


 

***
## Step 7: Sign Transaction (PSBT) 

$ `bdk-cli wallet --wallet wallet_name --descriptor $my_descriptor sign --psbt $PSBT`

> DON'T FORGET to COPY the PSBT for the next step

![](https://i.imgur.com/f4o4Ce8.gif)


 The output below confirms the command was successful.



```
{
  "is_finalized": true,
  "psbt": "cHNidP8BAFUBAAAAAak8uMR3UGkAGUKWsq8Mv45qg2fdD93JQRIsa2P0wFloAQAAAAD/////AQfgAQAAAAAAGXapFDRKD0jKFQ7CuQOBdmC5tosTpnAmiKwAAAAAAAEA3gIAAAAAAQFY9sVfEEbyjrHXSlxXDxL+71WOMnsPpVElwk+3E/J9vAAAAAAA/v///wIYZRIAAAAAABYAFBKYf7yF+ss6EFdw2rDZTfdLhep8eOABAAAAAAAWABQd6wBRnX8mBZiJYfvy/FGE+xKc+AJHMEQCIFSIkvEUI9yUgEw4JocRs1aiVsBlKKXrOQaQb3XFqR21AiBqiEVzCVVSRGjckyPDgAQBnOdSzBYR6Rw6KFcCP+E27wEhAwIlXdfM2WYnYa36Hp4MS6YkplBAgBsb1tYG9NiWFWTKzPYhAAEBH3jgAQAAAAAAFgAUHesAUZ1/JgWYiWH78vxRhPsSnPgiAgP80FpaWYQzGzCnNI9blXbei61YpAmtoezMRxpVvBJ6S0gwRQIhALWkBRSJzxuf0od4tPu3qFmEfJ2Y+/QBGtfjSFObWsPeAiA4QJx8Rk5pacrjHv5EOdw6RNHXcdtepFs+m0/Za/h0UQEiBgP80FpaWYQzGzCnNI9blXbei61YpAmtoezMRxpVvBJ6SxgTizKsVAAAgAEAAIAAAACAAAAAAAAAAAABBwABCGwCSDBFAiEAtaQFFInPG5/Sh3i0+7eoWYR8nZj79AEa1+NIU5taw94CIDhAnHxGTmlpyuMe/kQ53DpE0ddx216kWz6bT9lr+HRRASED/NBaWlmEMxswpzSPW5V23outWKQJraHszEcaVbwSeksAAA=="
}

```

***
### 7a: export signed psbt to environment variable

$ `export SIGNED_PSBT="Paste_PSBT_HERE"`

![](https://i.imgur.com/VJsl8zR.gif)





***
## Step 8: Broadcast Transaction 

 $ `bdk-cli wallet --wallet wallet_name --descriptor $my_descriptor broadcast --psbt $SIGNED_ PSBT`


![](https://i.imgur.com/yQZZk0d.gif)

 
 ```
The output below confirms the command was successful.

{
  "txid": "a0877b7ce91ea6d141ba63277673f5bdf0edfdd45f91a39ba1a1ace15f839b52"
}
 

 ```



 - Verify transcation in the memory pool on testnet [Mempool-testnet!](https://mempool.space/testnet)
 

> Finishing touch, run sync one more time and see that the balance has decreased.


 ***
## Resources

### Other Tutorials / Blog

- [ My Summer of Bitcoin Experience](https://blog.summerofbitcoin.org/sob-22-experience-bdk/)
### Useful links
- [BIP-32:  Hierarchical Deterministic Wallets](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki)
- [BIP: 39 - Mnemonic code for generating deterministic keys](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki)
- [BIP: 44 - Multi-Account Hierarchy for Deterministic Wallets](https://github.com/bitcoin/bips/blob/master/bip-0044.mediawiki)
- [BIP: 84 - Derivation scheme for P2WPKH based accounts](https://github.com/bitcoin/bips/blob/master/bip-0084.mediawiki)

- [BIP: 174 - Partially Signed Bitcoin Transaction Format](https://github.com/bitcoin/bips/blob/master/bip-0174.mediawiki)

- [What are Descriptors and miniscript?](https://blog.summerofbitcoin.org/miniscript-policy-descriptors-hidden-powers-of-bitcoin/)
- [Master Private  Key and Extended Private Key](https://bitcoin.stackexchange.com/questions/97242/bip39-tool-bip32-extended-private-key-vs-bip32-root-key)

- [MinscA Miniscript-based scripting language for Bitcoin contracts](https://min.sc)
