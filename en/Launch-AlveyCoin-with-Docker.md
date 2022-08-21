# How to run Alvey node with docker

## Quick start

This tutorial use Linux Ubuntu as example, OSX and windows platform are almost the same.

Suppose the reader has basic command line knowledge, and also have docker correctly installed and configured. If not, please learn about command line and Docker first.

For more details, please refer to [github](https://github.com/alveyproject/alvey-docker.git)

## Get docker image

You might take either way:

### Pull a image from Public Docker hub

```
$ docker pull alvey/alvey
```

### Or, build alvey image with provided Dockerfile

The Dockerfile can be downloaded on github: [Dockerfile](https://github.com/alveyproject/alvey-docker/blob/master/release/Dockerfile)

```
$docker build --rm -t alvey/alvey .
```

For historical versions, please visit [docker hub](https://hub.docker.com/r/alvey/alvey/)

## Prepare data path and alvey.conf

In order to use user-defined config file, as well as save block chain data, -v option for docker is recommended.

First chose a path to save alvey block chain data:

```
sudo rm -rf /data/alvey-data
sudo mkdir -p /data/alvey-data
sudo chmod a+w /data/alvey-data
```

Create your config file, refer to the example [alvey.conf]!(https://github.com/alveyproject/alvey/blob/1a926b980f03e97322c7dd787835bec1730f35d2/contrib/debian/examples/alvey.conf). Note rpcuser and rpcpassword to required for later `alvey-cli` usage for docker, so it is better to set those two options. Then please create the file ${PWD}/alvey.conf with content:

```
rpcuser=alvey
rpcpassword=alveytest
```
## Launch alveyd

To launch alvey node:

```
## to launch alveyd
$ docker run -d --rm --name alvey_node \
             -v ${PWD}/alvey.conf:/root/.alvey/alvey.conf \
             -v /data/alvey-data/:/root/.alvey/ \
             alvey/alvey alveyd

## check docker processed
$ docker ps

## to stop alveyd
$ docker run -i --network container:alvey_node \
             -v ${PWD}/alvey.conf:/root/.alvey/alvey.conf \
             -v /data/alvey-data/:/root/.alvey/ \
             alvey/alvey alvey-cli stop
```

`${PWD}/alvey.conf` will be used, and blockchain data saved under /data/alvey-data/

## Interact with `alveyd` using `alvey-cli`

Use following docker command to interact with your alvey node with `alvey-cli`:

```
$ docker run -i --network container:alvey_node \
             -v ${PWD}/alvey.conf:/root/.alvey/alvey.conf \
             -v /data/alvey-data/:/root/.alvey/ \
             alvey/alvey alvey-cli getinfo
```

For more alvey-cli commands, use:

```
$ docker run -i --network container:alvey_node \
             -v ${PWD}/alvey.conf:/root/.alvey/alvey.conf \
             -v /data/alvey-data/:/root/.alvey/ \
             alvey/alvey alvey-cli help
```
