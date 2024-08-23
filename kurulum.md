# 💾 0G Node Installation Guide

## **System Requirements**

| **Component** | **Minimum Requirements** | **Recommended Requirements** |
|---------------|--------------------------|------------------------------|
| **CPU**       | 4 Cores                   | 8 Cores                      |
| **RAM**       | 8 GB                      | 16 GB                        |
| **Storage**   | 500 GB SSD                | 1 TB SSD                     |

## 1. Update and Upgrade System Packages

Before starting, ensure your system has the latest updates:

```shell
sudo apt update && sudo apt upgrade -y
```

This command updates your package lists and installs the newest versions of your packages, including security updates.

## 2. Install Essential Packages

Install the necessary packages required for the setup:

```shell
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

These packages provide tools for downloading, building software, managing processes, and more.

## 3. Install Go (If Not Already Installed)

### 3.1. Download Go

Download the Go binary for your system architecture (assuming 64-bit):

```shell
cd $HOME
VER="1.21.3"
wget https://golang.org/dl/go$VER.linux-amd64.tar.gz
```

### 3.2. Install Go

Extract the downloaded archive and set up Go:

```shell
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf go$VER.linux-amd64.tar.gz
rm go$VER.linux-amd64.tar.gz
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source ~/.bash_profile
```

### 3.3. Create a Directory for Go Binaries

```shell
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

This directory will store custom Go binaries.

## 4. Set Environment Variables

Create a file (e.g., `.0gchain.env`) to store environment variables for your 0G node:

```shell
export WALLET="WALLETNAME"
export MONIKER="NODENAME"
export OG_CHAIN_ID="zgtendermint_16600-2"
export OG_PORT="26"
```

Load these variables into your shell profile:

```shell
source ~/.0gchain.env
```

## 5. Download and Build 0G Chain Binary

### 5.1. Clone the 0G Chain Repository

```shell
cd && rm -rf 0g-chain
git clone https://github.com/0glabs/0g-chain
cd 0g-chain
git checkout v0.3.1
```

### 5.2. Build the Binary

Build the 0G chain binary from the source code.

## 6. Configure 0G Chain Node

### 6.1. Initialize the Node

Initialize the node with your chain ID and moniker:

```shell
0gchaind config node tcp://localhost:${OG_PORT}657
0gchaind config keyring-backend os
0gchaind config chain-id $OG_CHAIN_ID
0gchaind init "$MONIKER" --chain-id $OG_CHAIN_ID
```

### 6.2. Download Genesis File and Address Book

Download the genesis file for the testnet:

```shell
rm ~/.0gchain/config/genesis.json && wget -P ~/.0gchain/config https://github.com/0glabs/0g-chain/releases/download/v0.2.3/genesis.json
```

### 6.3. Set Seeds and Peers

Configure the node to connect to the network:

```shell
SEEDS="81987895a11f6689ada254c6b57932ab7ed909b6@54.241.167.190:26656,010fb4de28667725a4fef26cdc7f9452cc34b16d@54.176.175.48:26656,e9b4bc203197b62cc7e6a80a64742e752f4210d5@54.193.250.204:26656,68b9145889e7576b652ca68d985826abd46ad660@18.166.164.232:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/" $HOME/.0gchain/config/config.toml
```

## 7. Set Custom Ports and Enable Prometheus

### 7.1. Modify Ports

Change the default ports to custom ports:

```shell
sed -i.bak -e "s%:1317%:${OG_PORT}317%g; s%:8080%:${OG_PORT}080%g; s%:9090%:${OG_PORT}090%g; s%:9091%:${OG_PORT}091%g; s%:8545%:${OG_PORT}545%g; s%:8546%:${OG_PORT}546%g; s%:6065%:${OG_PORT}065%g" $HOME/.0gchain/config/app.toml
```

### 7.2. Enable Prometheus and Disable Indexing

Enable Prometheus metrics and disable indexing:

```shell
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.0gchain/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.0gchain/config/config.toml
```

## 8. Configure Pruning

Set pruning parameters to manage disk usage:

```shell
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.0gchain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.0gchain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.0gchain/config/app.toml
```

## 9. Set Minimum Gas Price

Adjust the minimum gas price to prevent transaction failures:

```shell
sed -i "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0ua0gi\"/" $HOME/.0gchain/config/app.toml
```

## 10. Create a Systemd Service File

Create a systemd service file (`0gchaind.service`) to manage the 0G chain node process:

```shell
sudo tee /etc/systemd/system/0gchaind.service > /dev/null <<EOF
[Unit]
Description=Og Chain Node
After=network-online.target

[Service]
User=$USER
WorkingDirectory=$HOME/.0gchain
ExecStart=$(which 0gchaind) start --home $HOME/.0gchain
Restart=on-failure
RestartSec=5
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

## 11. Reset, Download Snapshot, and Start Node

### 11.1. Reset Node Data

Reset the node data:

```shell
0gchaind tendermint unsafe-reset-all --home $HOME/.0gchain
```

### 11.2. Download Snapshot (Optional)

Check if a snapshot is available for faster node initialization. You can find snapshots on [NodeJumper](https://app.nodejumper.io/0g-testnet/sync).

### 11.3. Start the Node

Enable and start the 0G chain node service:

```shell
sudo systemctl daemon-reload
sudo systemctl enable 0gchaind
sudo systemctl restart 0gchaind
```

### 11.4. Monitor Node Logs

Monitor the node logs for any errors or warnings:

```shell
sudo journalctl -u 0gchaind -f
```

Your 0G node should now be running and connected to the testnet.
