# Setting up an Ethereum Validator Node

## Table of Contents
1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Set Up the Execution Client (Eth1)](#set-up-the-execution-client-eth1)
4. [Set Up the Consensus Client (Eth2)](#set-up-the-consensus-client-eth2)
5. [Set Up the Validator](#set-up-the-validator)
6. [Generate Validator Keys](#generate-validator-keys)
7. [Start Staking](#start-staking)
8. [Monitoring and Maintenance](#monitoring-and-maintenance)

## Introduction

This guide will walk you through setting up an Ethereum validator node using Geth as the execution client and Prysm as the consensus client.

## Prerequisites


Install Docker and Docker Compose
  
1. Update and Upgrade the System:
     
  ```bash
    sudo apt update && sudo apt upgrade -y
  ```
2. Install Dependencies:
  ```bash
    sudo apt install -y build-essential git curl wget tmux
    sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    sudo apt update
    sudo apt install -y docker-ce
    sudo systemctl enable --now docker
    sudo usermod -aG docker $USER
  ```
3. Install Docker Compose:
    ```bash
    sudo curl -L "https://github.com/docker/compose/releases/download/v2.29.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    sudo chmod +x /usr/local/bin/docker-compose
    docker-compose --version
    ```

## Set Up the Execution Client (Eth1)

We'll use Geth as the Eth1 client.

1. Create a directory and navigate to it:

    ```bash
    mkdir -p ~/eth1 && cd ~/eth1
    ```

2. Create a `docker-compose.yml` file:

    ```bash
    nano docker-compose.yml
    ```

3. Paste the following content into the `docker-compose.yml` file:

    ```yaml
    services:
      eth1:
        image: ethereum/client-go:latest
        container_name: eth1-eth1-1
        volumes:
          - ./data:/root/.ethereum
          - /home/ubuntu/jwt-secret:/root/.ethereum/jwt-secret
        ports:
          - "8545:8545"
          - "8551:8551"
          - "30303:30303"
        command: [
          "--http",
          "--http.addr=0.0.0.0",
          "--http.vhosts=*",
          "--http.corsdomain=*",
          "--http.port=8545",
          "--http.api=eth,net,web3",
          "--authrpc.port=8551",
          "--authrpc.jwtsecret=/root/.ethereum/jwt-secret",
          "--syncmode=snap"
        ]
        networks:
          - eth_network

    networks:
      eth_network:
        external: true
    ```

4. Run the Eth1 client:

    ```bash
    docker-compose up -d
    ```

## Set Up the Consensus Client (Eth2)

We'll use Prysm as the Eth2 client.

1. Create a directory and navigate to it:

    ```bash
    mkdir -p ~/eth2 && cd ~/eth2
    ```

2. Create a `docker-compose.yml` file:

    ```bash
    nano docker-compose.yml
    ```

3. Paste the following content into the `docker-compose.yml` file:

    ```yaml
    services:
      beacon:
        image: gcr.io/prysmaticlabs/prysm/beacon-chain:stable
        container_name: eth2-beacon-1
        volumes:
          - ./data:/data
          - /home/ubuntu/jwt-secret:/home/ubuntu/jwt-secret
        command: [
          "--datadir=/data",
          "--execution-endpoint=http://eth1-eth1-1:8551",
          "--accept-terms-of-use",
          "--rpc-host=0.0.0.0",
          "--grpc-gateway-host=0.0.0.0",
          "--min-sync-peers=6",
          "--jwt-secret=/home/ubuntu/jwt-secret",
          "--checkpoint-sync-url=https://beaconstate.info",
          "--genesis-beacon-api-url=https://beaconstate.info"
        ]
        ports:
          - "4000:4000"
          - "3500:3500"
          - "13000:13000"
        networks:
          - eth_network

    networks:
      eth_network:
        external: true
        name: eth_network
    ```

4. Run the Eth2 client:

    ```bash
    docker-compose up -d
    ```

## Set Up the Validator

First, create a wallet:

1. Create a directory for the validator:

    ```bash
    mkdir -p /test_validator
    ```

2. Run the wallet creation command:

    ```bash
    docker run -it --name create_wallet \
      -v /test_validator:/data \
      gcr.io/prysmaticlabs/prysm/validator:stable \
      wallet create \
      --wallet-dir=/data/wallet \
      --accept-terms-of-use
    ```

Follow the prompts to create your wallet. Choose **"Imported Wallet"** when asked.

After creating the wallet, update the wallet password file:

```bash
echo "your_wallet_password" > /test_validator/wallet-password.txt
```

Replace "your_wallet_password" with the actual password you set for the wallet.

Now, run the validator:

    docker run -d --name eth2-validator-1 \
      --network eth_network \
      -v /test_validator:/data \
      -p 7500:7500 \
      gcr.io/prysmaticlabs/prysm/validator:stable \
      --accept-terms-of-use \
      --datadir=/data \
      --beacon-rpc-provider=http://eth2-beacon-1:4000 \
      --wallet-dir=/data/wallet \
      --wallet-password-file=/data/wallet-password.txt \
      --graffiti="USL Test" \
      --rpc-host=0.0.0.0 \
      --grpc-gateway-host=0.0.0.0

## Generate Validator Keys

To generate validator keys:

    docker run -it --rm \
      -v /test_validator:/data \
      gcr.io/prysmaticlabs/prysm/validator:stable \
      accounts create \
      --wallet-dir=/data/wallet \
      --wallet-password-file=/data/wallet-password.txt \
      --accept-terms-of-use

Follow the prompts to create your validator keys.

## Start Staking

For a real validator node, you need to deposit 32 ETH to your validator deposit contract address to start staking. In a test environment, this step can be skipped.

## Monitoring and Maintenance

Check logs:

    docker logs -f eth2-validator-1
    docker logs -f eth2-beacon-1
    docker logs -f eth1-eth1-1

Restart services:

    docker restart eth2-validator-1 eth2-beacon-1 eth1-eth1-1

Update clients: To update your clients, pull the latest images and restart the services:

    docker pull gcr.io/prysmaticlabs/prysm/validator:stable
    docker pull gcr.io/prysmaticlabs/prysm/beacon-chain:stable
    docker pull ethereum/client-go:latest
    docker restart eth2-validator-1 eth2-beacon-1 eth1-eth1-1
