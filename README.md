<div align="center">

#  👨🏻‍💻 **0G Storage Node Guide** 👨🏻‍💻

</div>

# Pre-Requirements 🛠

* Add 0G-Galileo-Testnet chain from here: https://docs.0g.ai/run-a-node/testnet-information

* Take faucet: https://faucet.0g.ai/


# Install All Required Dependencies

```
sudo apt-get update && sudo apt-get upgrade -y
```

```
sudo apt install curl iptables build-essential git wget lz4 jq make protobuf-compiler cmake gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev screen ufw -y
```

* Install rustup

```
curl https://sh.rustup.rs -sSf | sh
```
```
source $HOME/.cargo/env
```


* Install go

```
wget https://go.dev/dl/go1.24.3.linux-amd64.tar.gz && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf go1.24.3.linux-amd64.tar.gz && \
rm go1.24.3.linux-amd64.tar.gz && \
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc && \
source ~/.bashrc
```


# Clone the Repository

```
git clone https://github.com/0glabs/0g-storage-node.git
```

```
cd 0g-storage-node && git checkout v1.1.0 && git submodule update --init
```

* Build in release mode 

```
cargo build --release
```

# Set Configurations

```
rm -rf $HOME/0g-storage-node/run/config.toml
```

```
curl -o $HOME/0g-storage-node/run/config.toml https://raw.githubusercontent.com/Vaibhav994/0g-storage-node/main/config.toml
```


* Add Your Wallet's Private KEY in `config.toml`, ❗❗Dont Add **0X** before the key:

-Replace "abcd123_YourPrivateKEY_abc123" with your private key(without0x).

```
sed -i 's|miner_key = "Your_Wallet_Private_key_Without_0x"|miner_key = "abcd123_YourPrivateKEY_abc123"|' /root/0g-storage-node/run/config.toml

```
<img width="1549" height="179" alt="image" src="https://github.com/user-attachments/assets/a855be7d-7150-438d-8adf-652ac27bc032" />


# If u want to change RPC then follow this:

1. Get rpc from here - https://www.astrostake.xyz/0g-status

2. Chooose any rpc and replace "your_0glabs_rpc" in command below

```
sed -i 's|blockchain_rpc_endpoint = "https://evmrpc-testnet.0g.ai"|blockchain_rpc_endpoint = "your_0glabs_rpc"|' /root/0g-storage-node/run/config.toml

```

<img width="1424" height="161" alt="image" src="https://github.com/user-attachments/assets/e25a3c5f-1151-4274-9b66-0c1795b6f4b7" />


# Create a Systemd Service File

```
sudo tee /etc/systemd/system/zgs.service > /dev/null <<EOF
[Unit]
Description=ZGS Node
After=network.target

[Service]
User=$USER
WorkingDirectory=$HOME/0g-storage-node/run
ExecStart=$HOME/0g-storage-node/target/release/zgs_node --config $HOME/0g-storage-node/run/config.toml
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

* Reload

```
sudo systemctl daemon-reload
```

* Enable

```
sudo systemctl enable zgs
```

* Start service

```
sudo systemctl start zgs
```


# Managing Logs

```
sudo systemctl status zgs
```

* check Full Logs

```
tail -f ~/0g-storage-node/run/log/zgs.log.$(TZ=UTC date +%Y-%m-%d)
```

* Check block & Sync process - Match to the latest block on explorer

```
 while true; do     response=$(curl -s -X POST http://localhost:5678 -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"zgs_getStatus","params":[],"id":1}');     logSyncHeight=$(echo $response | jq '.result.logSyncHeight');     connectedPeers=$(echo $response | jq '.result.connectedPeers');     echo -e "logSyncHeight: \033[32m$logSyncHeight\033[0m, connectedPeers: \033[34m$connectedPeers\033[0m";     sleep 5; done
```



# Stop & Delete the service

```
sudo systemctl stop zgs
```

```
sudo systemctl disable zgs
sudo rm /etc/systemd/system/zgs.service
rm -rf $HOME/0g-storage-node
```



## Explorer & Useful Webs

* Explorer- (View your txs - Paste Your Address): https://chainscan-galileo.0g.ai/

* View Miner Details- (Add your wallet address at the end of the link): https://storagescan-galileo.0g.ai/miner/

DONE!!



