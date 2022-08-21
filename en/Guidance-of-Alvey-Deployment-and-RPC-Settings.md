# How to deploy a Alvey node

This guidance includes how to deploy, run and make RPC calls.

Suppose the readers are able to use Linux,Mac or Windows command line. If you are not familiar with command line, or just interested in using GUI wallet, please refer to another document about [Alvey Wallet Tutorial](Alvey-Wallet-Tutorial.md).

## Get Alvey Node

There are at least 4 ways to get a Alvey node, you can choose any way：

**1. Download the binaries**

If you do not care much about Alvey's source code, the easiest way to abtain Alvey node is to download the latest binaries on the [Alvey release page](https://github.com/alveyproject/alvey/releases). Currently it supports Linux, Window, OSX. It is highly recommanded to download the latest version. In this guidance, we will use v0.14.13 version as an example。

（Note: the version number you see might be different, here is v0.14.13, but other key words except for version are the same.）

* For **Mac** user：`alvey-0.14.13-osx64.tar.gz`
* For **Linux** users： `alvey-0.14.13-i686-pc-linux-gnu.tar.gz`(32bit) or `alvey-0.14.13-x86_64-linux-gnu.tar.gz`(64bit)
* For **Windows** users：`alvey-0.14.13-win32.zip`(32bit) or `alvey-0.14.13-win64.zip`（64bit）
* For **Raspberry Pi**：`alvey-0.14.13-arm-linux-gnueabihf.tar.gz`

Decompress after downloading, then you will get `alveyd` and `alvey-cli` under the path `<your path>/bin`.


**2. apt install for Linux users**

Please refer to [https://github.com/alveyproject/alvey/wiki/Setting-up-ALVEY-Ubuntu,-Debian-and-Mint-repository](https://github.com/alveyproject/alvey/wiki/Setting-up-ALVEY-Ubuntu,-Debian-and-Mint-repository)，Current support Ubuntu，Debian and Mint。

Raspberry Pi users can also use apt，please refer to [https://github.com/alveyproject/alvey/wiki/Installing-Alvey-on-Raspberry-Pi](https://github.com/alveyproject/alvey/wiki/Installing-Alvey-on-Raspberry-Pi)。

After installation, you should be able to run `alveyd` and `alvey-cli`directly in the terminal。

**3. Compiler from source code**

If you want to compile latest Alvey from source code, please pull the latest source from github : [https://github.com/alveyproject/alvey](https://github.com/alveyproject/alvey)。

The instruction for compilation : [https://github.com/alveyproject/alvey/blob/master/README.md](https://github.com/alveyproject/alvey/blob/master/README.md)。Currently we recommand to compile on Linux or OSX, while other platforms might need more configuration.

After compilation, you also get `alveyd` and `alvey-cli` under the path `<your path>/src/`

**4. Get Alvey node docker image**

Suppose user has docker environment installed correctly. ([what is docker?]())

The Alvey official docker image on docker hub is `alvey/alvey`, you can pull it by docker command ：

```
docker pull alvey/alvey:latest
```

All the four ways above cand get Alvey nodes. There are two important binaries which are related to deployment and RPC calls:

* `alveyd`：Alvey core program, i.e. Alvey fullnode program.
* `alvey-cli`：Alvey command line interface，interact with alveyd, achieve RPC calls.

## Deploy Alvey node

For the instruction for Alvey docker images, please refer to "[How to launch Alvey with docker](https://github.com/alveyproject/documents/blob/master/zh/Launch-Alvey-with-Docker.md)".

Other normal deployment methods are almost the same, here we take Ubuntu as an example. The commands in Mac and windows are exactly the same.


Run Alvey fullnode with `./alveyd`：

```
./alveyd -daemon
```

This with launch a alveyd daemon with the option `-daemon`. If user are interested in the event logs about smart contract, please add one more option `-logevents`. 

 More options can be found：

```
./alveyd -help
```

To stop running：

```
./alvey-cli stop
```

The default data path for different platforms:

* Linux：`~/.alvey/`
* Mac OSX：`~/Library/Application Support/Alvey`
* Windows:`%APPDATA%\Alvey`

You can also use `-datadir` to set your own data path。

Alvey node will sync all the historical block data for first launch, and saves to datadir. It might take some time. Then diagnostics for the node can be found under the data path `~/.alvey/debug.log`。

## Local RPC call

When the alvey node is running, we can use alvey command line interface `alvey-cli` to interact with `alveyd`, and make local RPC calls.

e.g.：

```
oldclock@raven:~/alvey-0.14.3/bin$ ./alvey-cli getinfo
{
  "version": 140300,
  "protocolversion": 70016,
  "walletversion": 130000,
  "balance": 0.00000000,
  "stake": 0.00000000,
  "blocks": 12126,
  "timeoffset": 0,
  "connections": 8,
  "proxy": "",
  "difficulty": {
    "proof-of-work": 1.52587890625e-05,
    "proof-of-stake": 886731.5868738915
  },
  "testnet": false,
  "moneysupply": 100028504,
  "keypoololdest": 1505186997,
  "keypoolsize": 100,
  "paytxfee": 0.00000000,
  "relayfee": 0.00400000,
  "errors": ""
}

```

To get all RPC command list:

```
./alvey-cli help
```

To get help for specific RPC command, use `./alvey-cli help <RPCcmd>`，e.g.：

```
./alvey-cli help getinfo
```
## JsonRPC settings

You can get the jsonrpc example by using (here we use `getinfo` as an example): `./alvey-cli help getinfo`

```
Examples:
> curl --user myusername --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "getinfo", "params": [] }' -H 'content-type: text/plain;' http://127.0.0.1:3889/
```

The example shows detailed format for jsonrpc content. However, it can only be called after setting `rpcuser` and `rpcpassword`. There are two ways to set the parameters, you can choose either.

Method 1：Create config file `~/.alvey/alvey.conf`，which should include:

```
rpcuser=test  #rpc user name, (necessary)
rpcpassword=test1234  #rpc password, (necessary)
# By default, only local jsonrpc is allowed
# Remote connection is allowed only when setting rpcallowip as following:
# both ipv4 and ipv6 can be set, e.g.:
#rpcallowip=192.168.77.51/255.255.255.0
#rpcallowip=1.2.3.4/24
#rpcallowip=2001:db8:85a3:0:0:8a2e:370:7334/96
```

For more config settings, please refer to ：[alvey.conf example](https://github.com/alveyproject/alvey/blob/1a926b980f03e97322c7dd787835bec1730f35d2/contrib/debian/examples/alvey.conf)

After creating the config file, please restart the node to finish settings.

Method 2：Restart Alvey node with following options:

```
./alveyd -daemon -rpcuser=test -rpcpassword=test1234 -rpcallowip=192.168.77.51/255.255.255.0
```

The meaning of options `rpcuser`, `rpcpassword` and `rpcallowip` are all the same with the config file above. 

## RPC call example

You can make remote RPC calls after the settings above. Here is an example: Suppose the alvey node is running on an Ubuntu machine, whose ip is 192.168.77.188, default port=3889. Now we try to make jsonrpc call in a remote macbook:

```
zhongwenbins-MacBook-Pro:~ zhongwenbin$ curl --user test:test1234 --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "getinfo", "params": [] }' -H 'content-type: text/plain;' http://192.168.77.188:3889/
{"result":{"version":140300,"protocolversion":70016,"walletversion":130000,"balance":0.00000000,"stake":0.00000000,"blocks":12197,"timeoffset":0,"connections":8,"proxy":"","difficulty":{"proof-of-work":1.52587890625e-05,"proof-of-stake":650787.7561123729},"testnet":false,"moneysupply":100028788,"keypoololdest":1505186997,"keypoolsize":100,"paytxfee":0.00000000,"relayfee":0.00400000,"errors":""},"error":null,"id":"curltest"}
```

The result is identical with local call.


You can also use tools like postman to get the same result：

![postman截图](https://s.alvey.site/uploads/e7398325af231cf42a708303af2a5098.png)


## Nginx settings（optional）

As the example above, you might find that it is quite complicated to make remote  jsonrpc, since you must include rpcuser, rpcpassword, as well as port in the command. If you don't want users to provide these parameters, we recommand using Nginx. The benefits for Nginx is not only about simplification, but also a good way to hide rpcuser, rpcpassword and port number. And you can filter some RPCs for security. Here we suppose the reader have installed Nginx and have basic knowledges about how to use it.

example:

* Alvey node is running on `192.168.77.188`,
* api proxy ip `192.168.77.51`, with Nginx installed

Instructions：

1.Set the `alvey.conf` of alvey node, remember to add the proxy ip to rpcallowip e.g.：

```
rpcuser=test
rpcpassword=test1234
rpcallowip=192.168.77.51/255.255.255.0
```

2.Setup Nginx ：

```
    server {
        listen       80;
        server_name  localhost;

        location / {
            proxy_pass http://192.168.77.188:3889;  #to port 3889
            proxy_set_header Authorization "Basic dGVzdDp0ZXN0MTIzNA==";  # its the base64 encode of test:test1234
        }
    }
```

3.Then you can make remote rpc call through proxy, without rpcuser or rpcpassword. e.g.：

```
zhongwenbins-MacBook-Pro:~ zhongwenbin$ curl  --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "getinfo", "params": [] }' -H 'content-type: text/plain;' http://192.168.77.51/
{"result":{"version":140300,"protocolversion":70016,"walletversion":130000,"balance":0.00000000,"stake":0.00000000,"blocks":12250,"timeoffset":0,"connections":8,"proxy":"","difficulty":{"proof-of-work":1.52587890625e-05,"proof-of-stake":651324.7815933984},"testnet":false,"moneysupply":100029000,"keypoololdest":1505186997,"keypoolsize":100,"paytxfee":0.00000000,"relayfee":0.00400000,"errors":""},"error":null,"id":"curltest"}
```

This is only a simple example. You might have different settings or tools instead.

## Useful commands and documents

If you have any questions during node deployment and RPC calls, here are some useful documents to refer to:

* For source code compilation issues： [https://github.com/alveyproject/alvey/blob/master/README.md](https://github.com/alveyproject/alvey/blob/master/README.md)。
* Command to check all alveyd options：`./alveyd -help`
* Command to get all RPC list： `./alvey-cli help`
* Get RPC help（like getinfo）： `./alvey-cli help getinfo`
* alvey.conf file example：[alvey.conf](https://github.com/alveyproject/alvey/blob/1a926b980f03e97322c7dd787835bec1730f35d2/contrib/debian/examples/alvey.conf)
