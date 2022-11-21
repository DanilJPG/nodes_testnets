## Lambda Mainnet

[Discord](https://discord.gg/lambdanetwork) | [Website](https://www.lambda.im/) | [Telegram](https://t.me/HelloLambda)
--- | --- | ---

Steps | Comments
--- | --- |
[Upgrade](https://github.com/DanilJPG/nodes_testnets/blob/main/Lambda%20Network/Lambda%20Mainnet.md#:~:text=Upgrade%20and%20install%20dependencies) | Check the version, if necessary update to the appropriate height blocks
[installing utilities](https://github.com/DanilJPG/nodes_testnets/blob/main/Lambda%20Network/Lambda%20Mainnet.md#:~:text=Upgrade%20and%20install%20dependencies) | server setup
[Installing GO](https://github.com/DanilJPG/nodes_testnets/blob/main/Lambda%20Network/Lambda%20Mainnet.md#:~:text=liblz4%2Dtool%20%2Dy-,Installing%20Go,-ver%3D%221.19.1%22%0Acd) | Go language is necessary to work with a binary file and unpack it
[Copying a repository](https://github.com/DanilJPG/nodes_testnets/blob/main/Lambda%20Network/Lambda%20Mainnet.md#:~:text=Cloning%20a%20repository) | Cloning the GitHub repository of a project
[Initializing](https://github.com/DanilJPG/nodes_testnets/blob/main/Lambda%20Network/Lambda%20Mainnet.md#:~:text=lambdavm%0Amake%20install-,Initializing,-lambdavm%20init%20%3Cyour_custom_moniker) | To generate configuration files
[Download genesis](https://github.com/DanilJPG/nodes_testnets/blob/main/Lambda%20Network/Lambda%20Mainnet.md#:~:text=id%20lambda_92000%2D1-,Download%20genesis,-wget%20%2DO%20/root) | The genesis stores the state of the chain
[Fixing the configure](https://github.com/DanilJPG/nodes_testnets/blob/main/Lambda%20Network/Lambda%20Mainnet.md#:~:text=Correct%20the%20configuration%20file) | Making changes to config.toml
[Prunning app.toml](https://github.com/DanilJPG/nodes_testnets/blob/main/Lambda%20Network/Lambda%20Mainnet.md#:~:text=config/config.toml-,Prinning,-pruning%3D%22custom%22%20%26%26%20%5C%0Apruning_keep_recent) | To save memory usage
[Service file](https://github.com/DanilJPG/nodes_testnets/blob/main/Lambda%20Network/Lambda%20Mainnet.md#:~:text=Create%20a%20service%20file) | Creating a service file
[Launch](https://github.com/DanilJPG/nodes_testnets/blob/main/Lambda%20Network/Lambda%20Mainnet.md#:~:text=user.target%0AEOF-,Launch,-systemctl%20daemon%2Dreload) | Start node 
[Wallet](https://github.com/DanilJPG/nodes_testnets/blob/main/Lambda%20Network/Lambda%20Mainnet.md#:~:text=auto%20%5C%0A%2D%2Dfees%205000ulamb-,Wallet,-%23%20create%20a%20wallet) | Creating and restoring a wallet
[Validator](https://github.com/DanilJPG/nodes_testnets/blob/main/Lambda%20Network/Lambda%20Mainnet.md#:~:text=Creating%20a%20validator) | Creating your node operator
[Useful commands](https://github.com/DanilJPG/nodes_testnets/blob/main/Lambda%20Network/Useful%20commands.md) | Here are commands for the validator, for node management and for the wallet
[State Sunc]() | Chain synchronization
#### Upgrade and install dependencies
```
sudo apt update && sudo apt upgrade -y && \
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```
#### Installing Go
```
ver="1.19.1"
cd $HOME
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
```

#### Cloning a repository 
```
git clone https://github.com/LambdaIM/lambdavm.git
cd lambdavm
make install
```

#### Initializing 
```
lambdavm init <your_custom_moniker> --chain-id lambda_92000-1 \
lambdavm config chain-id lambda_92000-1
```

#### Download genesis
```
wget -O /root/.lambdavm/config//genesis.json "https://raw.githubusercontent.com/LambdaIM/mainnet/main/lambda_92000-1/genesis.json"
#check

`sha256sum genesis.json`
# 1ff02001539bc1e9828fe170006f055c04df280c61c4ca9ecc9e7b6a272b7777  genesis.json
```

#### Correct the configuration file
```
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" /root/.lambdavm/config/config.toml

sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025ulamb\"/;" /root/.lambdavm/config/app.toml

PEERS=`curl -sL https://raw.githubusercontent.com/LambdaIM/mainnet/main/lambda_92000-1/peers.txt | sort -R | head -n 10 | awk '{print $1}' | paste -s -d, -`

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" ~/.lambdavm/config/config.toml

SEEDS=`curl -sL https://raw.githubusercontent.com/LambdaIM/mainnet/main/lambda_92000-1/seeds.txt | awk '{print $1}' | paste -s -d, -`
sed -i.bak -e "s/^seeds =.*/seeds = \"$SEEDS\"/" ~/.lambdavm/config/config.toml
```
#### Prinning
```
pruning="custom" && \
pruning_keep_recent="100" && \
pruning_keep_every="0" && \
pruning_interval="50" && \
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.lambdavm/config/app.toml && \
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.lambdavm/config/app.toml && \
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.lambdavm/config/app.toml && \
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.lambdavm/config/app.toml
```

#### Create a service file
```
sudo tee /etc/systemd/system/lambdavm.service > /dev/null <<EOF
[Unit]
Description=lambdavm
After=network-online.target
[Service]
User=$USER
ExecStart=$(which lambdavm) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

#### Launch
```
systemctl daemon-reload && \
systemctl enable lambdavm && \
systemctl restart lambdavm && journalctl -u lambdavm -f -o cat
```

#### Creating a validator 
```
lambdavm tx staking create-validator \
--amount 1000000000000000000ulamb \
--pubkey $(lambdavm tendermint show-validator) \
--moniker "garfield" \
--chain-id lambda_92000-1 \
--commission-rate "0.05" \
--min-self-delegation "1000000" \
--commission-max-rate "0.20" \
--commission-max-change-rate "0.01" \
--from garfield_wallet \
--gas=auto \
--fees 5000ulamb
```
#### Wallet 
```
# create a wallet
empowerd keys add $WALLET --keyring-backend os

# restore the wallet (after the command insert seed)
empowerd keys add $WALLET --recover --keyring-backend os

# export to metamask(view private key)
lambdavm keys unsafe-export-eth-key garfield_wallet

# export wallet from metamask
lambdavm keys unsafe-import-eth-key [account name] [private key]
```

#### Deleting
```
systemctl stop lambdavm && \
systemctl disable lambdavm && \
rm /etc/systemd/system/lambdavm.service && \
systemctl daemon-reload && \
cd $HOME && \
rm -rf .lambdavm lambdavm && \
rm -rf $(which lambdavm)
```
