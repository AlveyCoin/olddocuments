# Alvey Exchange Usage Guide and Info

## Setup

You can download a pre-built version of Alvey from our Github Release page:

https://github.com/alveyproject/alvey/releases/

Simply extract the .tar.gz linux-64bit archive. In the "bin" directory will be the two programs of interest, alveyd and alvey-cli

If you choose to compile it yourself instead, follow these steps on Ubuntu:

Install packages if needed:


```
sudo apt-get install build-essential libtool autotools-dev automake pkg-config libssl-dev libevent-dev bsdmainutils git cmake libboost-all-dev

sudo apt-get install software-properties-common

sudo add-apt-repository ppa:bitcoin/bitcoin

sudo apt-get update

sudo apt-get install libdb4.8-dev libdb4.8++-dev
```



```
git clone https://github.com/alveyproject/alvey --recursive
git checkout mainnet-ignition-v1.0.1 # or use the latest release
./autogen.sh
./configure --with-gui=no --enable-hardening
make -j3
```

Afterwards, in the "src" directory, there will be two programs of interest, `alveyd` and `alvey-cli`

## Startup

In order to startup alveyd, use the following:


```
./alveyd -daemon -staking=0
```

Additional options can be specified in `~/.alvey/alvey.conf`, you can add `-staking=0` in the alvey.conf file

Afterwards, you can check the status of the Alvey node:


```
./alvey-cli getinfo
```


It will show a block count. when the block count matches the latest block on our block explorer: [https://alveyscan.com](https://alveyscan.com) then that means that the node is synchronized. If it shows 0 blocks and 0 connections for more than a few minutes, contact Alvey staff for help. Syncing the blockchain will take around 10-30 minutes depending on connection speed


In order to shut down the node, simply use:

```
./alvey-cli stop
```

**NOTE:** `-staking=false` disables automatic staking in the wallet. This is **HIGHLY RECOMMENDED** for exchanges. Staking can make an unpredictable amount of coins unaccessible for 500 blocks (~20 hours) and so it is recommended that exchanges do not participate in staking.

## Wallet Backup

Make sure to always back up your wallet! The location of the wallet file on Linux is `~/.alvey/wallet.dat`. Make sure to shut down the node before making a backup to avoid data corruption.

The wallet can also be encrypted on-disk:


```
./alvey-cli encryptwallet "your strong password"
```

The Alvey node will automatically stop itself in order to encrypt the wallet. You will need to execute `./alveyd -daemon -staking=false` again afterwards to start the node again

**IMPORTANT:** after encrypting the wallet **YOU MUST** make a new backup of the wallet.dat file. Failing to do so can result in **LOSS OF COINS**. If the password to the encrypted wallet is lost, there is **NO WAY TO RECOVER THE COINS**. Make sure to take backups of both wallet.dat, as well as the password used for encryption!

In order to unlock the wallet:


```
./alvey-cli walletpassphrase "your strong password" XXX
```


XXX is how many seconds to keep the wallet unlocked for. So, specifying that as "1000" would mean keep the wallet unlocked for 1000 seconds.

For an exchange it is probably best to not use wallet encryption on the hot wallet. In the above example, after 1000 seconds have passed, it is necessary to unlock the wallet again. This may cause some problems depending on your exact exchange systems

**NOTE:** Unlocking the wallet is only required to send coins. If you only need to receive coins and track transactions, this can be done with an encrypted and locked wallet

## Receiving coins

In order to generate a new address:


```
./alvey-cli getnewaddress ""
```

This will generate a unique address every time it is executed. Note Alvey addresses start with Q for standard pubkeyhash addresses, and start with M for multi-sig addresses

To validate an address is valid:


```
./alvey-cli validateaddress "address here"
```

In order to see the coins received by a particular address (note the address must be owned by this wallet. This does not allow checking addresses not owned by this wallet):


```
./alvey-cli getreceivedbyaddress "address here" MINCONF
```

In this case, `MINCONF` is how many confirmations the transaction must have had on the blockchain before it will be included in this number.

Minimum confirmations recommended by Alvey:

- 20 confirmations for very large amounts and for any amount during the first month of the mainnet launch
- 10 confirmations for small and medium amounts after 1 month when the network is proven stable

To get all transactions (including unconfirmed!) currently tracked by the wallet use this function:


```
./alvey-cli listtransactions "*" COUNT SKIP
```

COUNT and SKIP can be used for paging through transactions. Using a very large count value may cause the node to perform the request slowly, and so it is recommended to never use a COUNT that is more than 100. SKIP is used to skip the first number of results. It is not recommended to rely only on this for the accounting of an exchange. It is possible that while you are iterating to discover the full transaction list, that someone sends a transaction to the wallet that will be missed. Also, this method includes **UNCONFIRMED** transactions and must be used with caution.

**WARNING**: Bitcoind and thus alveyd has some account functionality so that different addresses can use different accounts. **DO NOT RELY ON THIS**. This feature is deprecated and has many security problems, and will be removed in the next release.

## Sending Coins

It is not easily possible to dictate what address coins should be sent from. If your exchange system relies on this, you should make a workaround in your system so that this is not necessary.

In order to send coins:


```
./alvey-cli sendtoaddress "address to send to" AMOUNT "comment about tx" "comment about address" SUBTRACTFEE
```

The "comment" arguments can be left blank if unneeded. These comments are saved within your wallet file and are used by some exchanges to track internal withdraw and user IDs.

The SUBTRACTFEE option can be either "true" or "false". When this option is true, it subtracts the fee from the amount sent. So, if you do


```
./alvey-cli sendtoaddress "address" 10 "" "" true
```

And the network fee is 0.1 Alvey, then the actual amount that the user will receive will be 9.9 Alvey. If SUBTRACTFEE is instead set to false, then they will receive 10, but your wallet will have paid 0.1 Alvey for the network fee.

**NOTE:** Exchange users should not be capable of sending coins directly to a smart contract address (one starting with 0x). These addresses will be rejected by sendtoaddress and proper usage requires significantly more caution to properly use.

## Parameters

- Recommended tx fee: 400000sat or 0.004 Alvey per kilobyte of tx data (2kb is reasonable transaction size expectation, but it can sometimes be more)
- Recommended confirmations: 20 for the first month, 10 afterwards
- Recommended minimum withdraw amount: 0.01 Alvey
- P2P Network Port: 3888 (port forwarding is not required or recommended, but the wallet must be able to access other external nodes through this port to download the blockchain)
