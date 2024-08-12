
## 1. Install Rust
Ensure Go is installed first. Then install Rust.
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh source $HOME/.cargo/env
```

## 2. Install cargo-generate for scaffolding contracts 
```
cargo install cargo-generate
```
## 3. Install wasm-opt for optimizing your contracts 
```
sudo apt-get install binaryen 
```

## 4. **Setting Up `wasmd` on the Remote Server**


You have two main options for setting up `wasmd`:

a.  **Manual Installation**: Compile `wasmd` directly on your server.
b.  **Using Docker**: Run `wasmd` in a Docker container.

# **Installation Option 1: Manual**


### Clone the wasmd repository 
```
git clone https://github.com/CosmWasm/wasmd.git
cd wasmd
```
 ### Compile and install wasmd 
 ```
 make install
```
### Verify the installation 
```
wasmd version 
```

After installing `wasmd`, you can start your node:


```
wasmd init mynode --chain-id mychain
wasmd start
```

# **Installation Option 2: Using Docker**


If Docker is installed on your server, you can pull and run the`wasmd`Docker image:    

## Pull the latest wasmd Docker image 
```
docker pull cosmwasm/wasmd:latest
```
## Set up the node 
```
docker run --rm -it \
-e PASSWORD=your_password \
--mount type=volume,source=wasmd_data,target=/root \
cosmwasm/wasmd:latest /opt/setup_wasmd.sh your_address
```

## Run the node 
```
docker run --rm -it -p 26657:26657 -p 26656:26656 -p 1317:1317 \
 --mount type=volume,source=wasmd_data,target=/root \
cosmwasm/wasmd:latest /opt/run_wasmd.sh `       ``
```


## 5. **Deploying and Testing Smart Contracts**


After setting up`wasmd`on your remote server, you can deploy and test your smart contracts similarly to how you would locally. You’ll need to:  
a.  **Upload your code**: Use`scp`or any other file transfer method to move your smart contract`.wasm`file to the server.
   ```
scp ./path_to_your_wasm_file.wasm your_username@your_server_ip:/path_on_server/
```
b.  **Deploy the Contract**: Use the`wasmd`CLI on your server to deploy the contract.
```
wasmd tx wasm store /path_on_server/your_contract.wasm --from your_wallet_name --gas auto --fees 1000ucosm --node http://localhost:26657
```
c.  **Interact with the Contract**:
   Instantiate, execute, and query the contract as needed using the`wasmd`CLI, just like you would locally.        ``

## 5. **Setting Up Remote Access**

If you want to interact with`wasmd`running on your remote server from your local machine, make sure to configure your firewall to allow access to the necessary ports (e.g., 26657 for RPC). 

You can then interact with the node using the RPC endpoint:  
```
wasmd query wasm contract-state smart <contract_address> '{"get_count":{}}' --node
```
