# Building Alvey Core

### Installing dependencies (This will work for Ubuntu and Debian)

```bash
sudo apt-get install build-essential libtool autotools-dev automake pkg-config libssl-dev libevent-dev bsdmainutils git cmake libboost-all-dev libgmp3-dev
```

## Compiling Alvey Core from source

### From Github source:



```bash
git clone https://github.com/alveyproject/alvey --recursive
cd alvey && make -C depends/
DEPENDS="$(pwd)/depends/x86_64-pc-linux-gnu"
./autogen.sh
./configure --prefix=$DEPENDS
make
make install
```

This will install all the Alvey binaries on `depends/x86_64-pc-linux-gnu`

### From a release file:

```bash
wget https://github.com/alveyproject/alvey/archive/refs/tags/mainnet-fastlane-v0.20.2.tar.gz
tar -xpf mainnet-fastlane-v0.20.2.tar.gz && cd alvey-mainnet-fastlane-v0.20.2 
git clone --recursive https://github.com/alveyproject/cpp-eth-alvey.git src/cpp-ethereum
make -C depends/
DEPENDS="$(pwd)/depends/x86_64-pc-linux-gnu"
./autogen.sh
./configure --prefix=$DEPENDS
make
make install
```

This will install all the Alvey binaries on `depends/x86_64-pc-linux-gnu`

## Running Alvey Core

Type from a terminal: 

`alveyd -daemon`

Also, make sure to create a alvey.conf with your credentials if needed for accessing RPC calls.

This is a sample alvey.conf file

```bash
rpcuser=alvey
rpcassword=coin
server=1
rpcallowip=127.0.0.1
logevents=1
daemon=1
```


# ARC20 Tokens on Alvey Core

## Enabling Log events

add `logevents=1` to the alvey.conf file before launching the daemon

Run Alvey like this:

`alveyd -daemon -logevents -txindex=1`

If Alvey has already synced, you'll need to reindex blocks:

`alveyd -daemon -reindex` 

## ARC20 token deep dive

https://docs.alvey.site/en/Raw-ARC20-Transaction-implementation-guide

https://docs.alvey.site/en/ARC20-integration.html

