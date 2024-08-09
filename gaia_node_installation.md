# CosmosHub (Gaia) Node Installation Guide

Chain ID: cosmoshub-4 | Current Node Version: v18.1.0

## Table of Contents
1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Install and Configure Go](#install-and-configure-go)
4. [Node Installation](#node-installation)
5. [Node Configuration](#node-configuration)
   - [Initialize Node](#initialize-node)
   - [Download Genesis](#download-genesis)
   - [Configure Seed](#configure-seed)
   - [Configure Address Book](#configure-address-book)
   - [Configure Persistent Peers](#configure-persistent-peers)
6. [Node Launch](#node-launch)
   - [Create Service File](#create-service-file)
   - [CosmosHub Snapshot Server Setup](#cosmoshub-snapshot-server-setup)
   - [How to Process CosmosHub Snapshot](#how-to-process-cosmoshub-snapshot)
   - [Start Node Service](#start-node-service)
7. [Troubleshooting after starting Node](#troubleshooting-after-starting-node)

## Introduction

This is a guide to install and troubleshoot a Gaia node from scratch.


## Prerequisites

Update packages, ready server for installation and install Go.


## Install and Configure Go

Skip this step if you already have Go.

### Install Go

We will use Go `v1.21.1` as example here. The code below also cleanly removes any previous Go installation.

```
sudo rm -rvf /usr/local/go/
wget https://golang.org/dl/go1.21.1.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.21.1.linux-amd64.tar.gz
rm go1.21.1.linux-amd64.tar.gz
```

### Configure Go

Unless you want to configure in a non-standard way, then set these in the `~/.profile` file.

```
nano ~/.profile
```

Paste the below at the bottom of the `~/.profile` file.
```
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
```

Close `~/.profile` file and run:

```
source ~/.profile
```

## Node Installation

Install the current version of node binary.

```
git clone https://github.com/cosmos/gaia cosmos
cd cosmos
git checkout v18.1.0
make install
```

## Node Configuration

### Initialize Node

 You can use `USL_COSMOS` as your moniker.

```
gaiad init USL_COSMOS --chain-id cosmoshub-4
```


### Download Genesis

The genesis file link below is Polkachu's mirror download. The best practice is to find the official genesis download link.

```
wget -O genesis.json https://snapshots.polkachu.com/genesis/cosmos/genesis.json --inet4-only
mv genesis.json ~/.gaia/config
```


### Configure Seed

Using a seed node to bootstrap is one of the best practices to boostrap a node. 

```
sed -i 's/seeds = ""/seeds = "ade4d8bc8cbe014af6ebdf3cb7b1e9ad36f412c0@seeds.polkachu.com:14956"/' ~/.gaia/config/config.toml
```


### Configure Address Book

Sometimes node operators will face peering issues with the rest of the network. Often it can be solved with a good seed node or a list of stable persistent peers.However, when everything else fails, you can choose to trust the below `addrbook.json` file to bootstrap your node's connection with the network. Stop your node, download and replace your `addrbook.json` with the steps below, and restart your node.

```
cd ~/.gaia/config
wget -O addrbook.json https://snapshots.polkachu.com/addrbook/cosmos/addrbook.json --inet4-only
mv addrbook.json ~/.gaia/config
```

Reference:  [addrbook](https://www.polkachu.com/addrbooks/cosmos) 




### Configure Persistent Peers

Using Persistent Peers to bootstrap is the best practice. Here is a list of 91 active peers as observed by Polkachu's state-sync server in real-time. Add them to your `config.toml` if you have trouble finding peers.

```
f4ca20817f5efc2d30a1ee381afdb0c5e77db20d@3.217.133.209:26656,ee6dc07d6b2259355a70f51398d47ea6b798a0f8@141.94.255.35:23050,592c57d4fceff75da5c2d7de170888a3cef18793@13.229.171.113:26656,88bd49450f1e9ffef6e272b2002862b2c012c315@95.217.43.189:14956,7b01e10eb6fcd27e1f99611e1bd56618e429f8c5@100.20.184.195:26656,4a038579e4af256118f3dc7925747c8b8f5321b8@142.93.235.166:26090,1997e68bf205bedeed0c4723786bf03464987dc1@77.87.108.21:26656,04f2af4755c1480ec3ba067273499fafa20b6687@23.91.85.92:26656,0add711ee2dcedcfb4c575aa1ace3f4995c8d731@170.64.218.141:26090,48b325eec2e80504665b421ede1499f8258c8e86@93.159.130.37:26656,580e2ac5b0fc4e17c80f1dc669ac2c0ea1186b2c@52.79.47.98:26656,388a20896c66bc2cb6ad03f2569fbdc71dcd843f@18.210.143.47:26656,37dfe1ec33e9f88f378a61a32462d57d2baa5e74@65.108.99.140:26656,01e9e9bd09fb7460b7f961a51406a49d6fb800dd@95.217.150.168:26656,0ecd1883e4a0bfad8021a8caac404b33e820276c@148.251.121.154:26656,2cd5f9fb5d6cb69fb0688f2fd0d78dbec4229a56@85.17.54.6:26656,fe21dd474640247888fc7c4dce82da8da08a8bfd@135.181.113.227:26656,1da54d20c7339713f1d6d28dd2117087dd33d0ca@5.9.59.145:26656,ac75ee05107fae2356ba5ee8e71516c9362aefea@176.9.147.152:26656,bc73bedb1044e1453a2d7651ab32be4000d3d958@34.195.124.95:26656,f956e0b6368d9712f173a8459679fd4413a3a3d1@192.69.220.226:26656,b0dc1b37f1c6e1849d74385cca8257ca73c28f87@51.81.154.152:26656,a141795ff85682aed0338a9c6ce8f08a90c9d289@66.165.248.66:26656,89367eebb50a7da333d80dce71b5d5020eb01f84@23.90.70.43:49656,8837adba2117455dd4ff75b9307028c36342e12a@141.164.38.26:11000,c7b5e2e7d84930128e7e5f594b299b20c8669463@51.210.155.88:23050,ec39fae6c161dea476d50bbaffa1ca0e7fbcb21f@65.21.200.7:1100,be43400a8688132bd225b8cf9aad16042a37dac1@139.59.75.73:26090,a834e4acbccbdf9502da24efadcd38676a431dbc@52.73.168.104:26656,7038b5a025e1408d76ba6a3f7e6a6e459f386f87@52.203.105.100:26656,4ddba29a7dfa740a4edeb5c620c963f67f951e1d@5.9.72.212:2000,d9fb37fbf37ba46646c320307b39b4aab5847083@65.108.40.61:21656,ee636b0fe493b05dc03da440291e68c475213784@51.91.214.93:14956,e4c45a68bad4f371781d4e2af67b5397af69e889@37.27.131.10:26656,c843429ef1b4a551dab64901991c41af2bc4454f@65.108.130.171:26656,42541224049c05da9b37631b618d7f96875e320a@185.137.173.156:26656,2363c2b8f699aa861d50adbedb55a79dec4b01ad@54.82.200.23:26656,e66d3c9d0f420744d34b1f08f0fed9cfcf433460@57.129.32.86:26656,5b4529df65f9c1006d51472a827f1deb23825ba2@89.33.22.102:14656,28c8b67b4334a62d5330e08d6c2d273774bba328@65.109.63.97:26656,0b78e4ab3dd109e51f36d50be323209857fe7513@141.94.242.39:23050,e6d729935d77865bf79fc051b2a9d1a263f1fcdc@37.27.56.238:5000,f52b6ca356060842431aa96392af4e9fdeaec436@67.209.53.70:26656,7dd34d8d3880bc48eff3e47b941d06bd1941a962@93.115.25.106:26656,7387927874d1950a36e895c0cdf2d198b0bfaef8@135.181.174.106:26656,25be76c88681eeed9150bb994b07082dafb4fa8d@93.159.130.35:26656,8220e8029929413afff48dccc6a263e9ac0c3e5e@204.16.247.237:26656,7b15dce221b13ca353187b4f7219a94db6b71ad3@185.119.118.109:2000,e829d4764a5cecc44b3414777853b34407b36601@198.244.202.140:26656,396694706ef7535dfc3d01e0ecd5a9b7a2525356@104.199.219.164:26656,a1dc1d64c4412e0f9bf9ff6111b18fd68bc38d05@46.101.130.205:26090,ddf4767a239d39e319a39437086673dc8b502f0b@54.36.231.142:26656,1a2ec6b643e4f6fda476dc94f579c2035e1de33f@178.128.238.183:26090,155ee9292bf5212c9577841018131428939e0a85@128.199.128.15:26090,eb37f31d01f78ba752382eaea614be3971269dd4@87.59.218.30:26656,8352e66b77b4a16bac0dcb82746bfc09cbb11e6b@15.235.115.156:11000,df316ea058d7245d913bce493a1819d4d9f2fdf0@202.8.11.21:26656,493f7e6de75458bb9f4e01efb5310dd82b250ec2@115.179.102.161:25656,ce345ae23f0d16e5d843c1f84f8e410d732b5bd8@46.105.71.65:26656,8e5f11bac96bf72bc4dbbf6b7b9107a4de0ba12d@178.18.251.12:26656,620c1ff08988ac2a1014f0964c794cc0a9698899@204.16.247.238:26656,98bdd5fa7e241d2691e245fc1d2361af0b406f37@158.220.88.140:14956,48c5af84afc9e25f62a7189f0260fd907aac5f68@204.16.247.246:26656,27ad834c62dbefc5beb74be7575515927bd07c58@37.120.245.50:26656,8e1d67d00282c0c785aeef9c47794e1f1f92e182@54.179.214.184:26656,023eabdd8c577532d54eb4fdafe84e84e08e538f@67.209.54.175:26656,7fbd001395634160be66bbcf08651fed5e0b9b64@162.19.18.137:33042,48fc4fe58d5392bda805212ba0c8e4e772dba1f9@142.132.158.93:14956,baeb07bdecbbe1741a9ed6ca7c320c365eedbdc1@5.9.95.101:14956,6ea2ef7d3dd5d6967708a0b31eed85ba090a90a1@65.108.121.190:12010,8f25b414e80b7fc8d8c07ef2bb78dd8dcd64be3a@51.79.78.30:26656,684bb77376ecdc36df0da97941f507bdc6151a8f@178.63.141.105:26656,322efd4fdc72a189a2fc8b2b597927831df2bbed@146.70.243.137:26656,2441e90fcb341fcd5bebec15b54e346cdca64a9b@135.148.123.8:14956,3149f737b92fa81c9acabf55d9500309a5cc378c@37.120.245.112:26656,2a7d5c1d7f0325f773d97d2a9a804153157335c8@5.9.86.153:26656,7209ec14a9f831baef8f16af9cd7ed69b2e6fc98@95.217.144.107:14956,b858ca4f3fed2c36b949cf67188b126e2542a39a@135.181.215.115:26726,0806f078affcea7ca4ae0ba89156b97114fbfbea@5.10.19.49:26656,72c55726bc6d46fd1613e365a7e6251ec01255d4@162.55.245.228:26656,e726816f42831689eab9378d5d577f1d06d25716@164.152.162.21:26656,e5678237a2c82b9d5458bd06c78e8ae452091e4c@3.21.100.135:26656,160524d2ad8e475dadfa64db9c341767064b5e92@148.251.183.254:12010,45f55db7f67614a1f95b46c7e1da101c18860698@37.120.245.41:26656,da54fbdcc95f499b605b7e75bbee32bbf9600bae@34.146.230.162:26656,9c116194f25fd0d146019f171ef0f49904dcc586@167.86.98.230:26656,fe6b75dc9d48ae6b951936831604472501045134@45.92.11.100:26656,6164c915e4a0a2551d78521fbc82b67672b0aac1@18.142.226.196:26656,35cfaafed2758c6eb3a1a418a6e2111e4fec8668@65.109.82.144:28656,9d899bc09a5612062fd172c62391e4e671c4d834@18.140.170.32:26656,6ecca845883e9273062ee515d2657080e6539d9e@65.109.32.148:26726
```

Here is a script for you to update `persistent_peers` setting in `config.toml`. Stale peers can cause node inefficiency over time, so the script below selects up to 5 random live peers.

```
PEERS=e726816f42831689eab9378d5d577f1d06d25716@164.152.162.21:26656,baeb07bdecbbe1741a9ed6ca7c320c365eedbdc1@5.9.95.101:14956,8220e8029929413afff48dccc6a263e9ac0c3e5e@204.16.247.237:26656,fe6b75dc9d48ae6b951936831604472501045134@45.92.11.100:26656,a141795ff85682aed0338a9c6ce8f08a90c9d289@66.165.248.66:26656
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.gaia/config/config.toml

```
Reference: [persistent\_peers](https://www.polkachu.com/live_peers/cosmos).



## Node Launch

### Create Service File

Create a `cosmos.service` file in the `/etc/systemd/system` folder with the following code snippet. Make sure to replace `USER` with your Linux user name. You need sudo previlege to do this step.

```
[Unit]
Description="cosmos node"
After=network-online.target

[Service]
User=USER
ExecStart=/home/USER/go/bin/gaiad start
Restart=always
RestartSec=3
LimitNOFILE=4096
Environment="DAEMON_NAME=gaiad"
Environment="DAEMON_HOME=/home/USER/.gaia"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="UNSAFE_SKIP_BACKUP=true"

[Install]
WantedBy=multi-user.target
```


### CosmosHub Snapshot Server Setup

Snapshot providers usauallly take one node snapshot every day. Identify a good provider like Polkachu. The snapshot is designed for node opeartors to run an efficient node on CosmosHub chain. To make the snapshot as small as possible while still viable as a validator, the following settings are used to save on the disk space. It might be helpful for you to sync with a snapshot periodically because Tendermint chain storage grows over time regardless of the pruning.

Make these changes to `app.toml`:

#### app.toml

```
# Prune Type
pruning = "custom"

# Prune Strategy
pruning-keep-recent = "100"
pruning-keep-every = "0"
pruning-interval = "10"
```

Make these change to `config.toml`:

#### config.toml

```
indexer = "null"
```

## How To Process CosmosHub Snapshot

Install lz4 if you dont have it (other formats you can use are zstd, etc)

```
sudo apt install lz4
```

Download the snapshot

```
wget -O cosmos_21642519.tar.lz4 https://snapshots.polkachu.com/snapshots/cosmos/cosmos_21642519.tar.lz4 --inet4-only
```

Stop your node

```
sudo service cosmos stop
```

Reset your node. This will erase your node database. If you are already running validator, be sure you backed up your `priv_validator_key.json` prior to running the the command. The command does not wipe the file. However, you should have a backup of it already in a safe location. Also, be WARNED: If you use this snapshot on a validator node during a chain halt, make sure you back up `priv_validator_state.json` and then replace it after the snapshot is extracted but before you start the node process. This is very important in order to avoid double-sign. 

```
# Back up priv_validator_state.json if needed
cp ~/.gaia/data/priv_validator_state.json  ~/.gaia/priv_validator_state.json

# Reset node state
gaiad tendermint unsafe-reset-all --home $HOME/.gaia --keep-addr-book
```

Decompress the snapshot to your database location. You database location will be something to the effect of `~/.gaia` depending on your node implemention.

```
lz4 -c -d cosmos_21642519.tar.lz4  | tar -x -C $HOME/.gaia
```

Reference: [snapshot](https://www.polkachu.com/tendermint_snapshots/cosmos) . IMPORTANT: If you run a validator node and the chain is in halt, it is time to replace the `priv_validator_state.json` file that you have backed up.

```
# Replace with the backed-up priv_validator_state.json
cp ~/.gaia/priv_validator_state.json  ~/.gaia/data/priv_validator_state.json
```

If everything is good, now restart your node


### Start Node Service

```
# Enable service
sudo systemctl enable cosmos.service

# Start service
sudo service cosmos start

# Use this to restart service, if started earlier
sudo systemctl restart cosmos

# Remove downloaded snapshot to free up space
rm -v cosmos_21642519.tar.lz4

# Make sure that your node is running
sudo service cosmos status

# Check logs
sudo journalctl -fu cosmos
# Or
sudo journalctl -u cosmos -f
```

## Troubleshooting after starting Node

1. Set ```min gas price``` in ```app.toml```: Fixed this by setting it to ```0.025uatom``` standard value.
2. Address book issue - Follow steps to solve this added to this section here:[Configure Address Book](#configure-address-book)
3.  Seed issue solved by adding seeds to the p2p section of ```config.toml``` as described here: [Configure Seed](#configure-seed)
4.  Persistent peer issue solved by pasting the peers in this section into the p2p section of ```config.toml```, steps: [Configure Persistent Peers](#configure-persistent-peers)
5.  Edit ```cosmos.service``` to set Restart to `on-failure` and LimitNOFile to `infinity` and ```sudo systemctl daemon-reload```
6.  Check external iPv4 address with ```curl -4 ifconfig.me``` and set it in ```config.toml```specifically the ```external address``` parameter.
7.  In ```config.toml```, remove problematic persitent peers that keep disconnecting (we had set 91 peers, so identify bad ones in the log and remove them).
