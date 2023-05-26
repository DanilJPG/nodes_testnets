## Dymension


[Discord](https://discord.gg/dymension) | [Website](https://dymension.xyz/) | [Portal](https://portal.dymension.xyz/)
--- | --- | ---

Steps | Comments
--- | --- |
[Server Preparation]() | Check the version, if necessary update to the appropriate height blocks,server setup, Go language is necessary to work with a binary file and unpack it
[Working with a binary file and setting up]() | Cloning the GitHub repository of a project,To generate configuration files,The genesis stores the state of the chain,Making changes to config.toml,Creating a service file
[Creating a validator and generating a wallet]() | Creating and restoring a wallet,Creating your node operator
[Useful commands]() | Here are commands for the validator, for node management and for the wallet
[Possible Errors]() | Here are possible errors related to the node and the validator


***

### 1.Подготовка сервера - Server Preparation 
#### Обновление и установка зависимостей - Upgrade and install dependencies
```Bash
sudo apt update && sudo apt upgrade -y && \
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```
#### Installing GO
```Bash
ver="1.19.1" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

### 2.Работа с бинарным файлом и настройка - Working with a binary file and setting up
#### Cloning a repository 
```Bash
git clone https://github.com/dymensionxyz/dymension && cd dymension
git checkout v0.2.0-beta
make install
```
#### Initializing
```Bash
dymd init <name_moniker> --chain-id 35-C
dymd config chain-id 35-C
dymd config keyring-backend os
```
#### Download genesis and addrbook
```Bash
wget -O $HOME/.dymension/config/genesis.json "https://raw.githubusercontent.com/dymensionxyz/testnets/main/dymension-hub/35-C/genesis.json"
wget -O $HOME/.dymension/config/addrbook.json "https://share.utsa.tech/dymension/addrbook.json"
```
#### Correct the configuration file
```Shell
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025udym\"/;" ~/.dymension/config/app.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.dymension/config/config.toml

seeds="f97a75fb69d3a5fe893dca7c8d238ccc0bd66a8f@dymension-testnet.seed.brocha.in:30584"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.dymension/config/config.toml

peers="ebc272824924ea1a27ea3183dd0b9ba713494f83@dymension-testnet-peer.autostake.net:27086,9111fd409e5521470b9b33a46009f5e53c646a0d@178.62.81.245:45656,f8a0d7c7db90c53a989e2341746b88433f47f980@209.182.238.30:30657,1bffcd1690806b5796415ff72f02157ce048bcdd@144.76.67.53:2580,c17a4bcba59a0cbb10b91cd2cee0940c610d26ee@95.217.144.107:20556,e6ea3444ac85302c336000ac036f4d86b97b3d3e@38.146.3.199:20556,b473a649e58b49bc62b557e94d35a2c8c0ee9375@95.214.53.46:36656,db0264c412618949ce3a63cb07328d027e433372@146.19.24.101:26646,281190aa44ca82fb47afe60ba1a8902bae469b2a@dymension.peers.stavr.tech:17806,d8b1bcfc123e63b24d0ebf86ab674a0fc5cb3b06@51.159.97.212:26656,55f233c7c4bea21a47d266921ca5fce657f3adf7@168.119.240.200:26656,139340424dddf85e54e0a54179d06875013e1e39@65.109.87.88:24656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.dymension/config/config.toml
```
#### Prunning `app.toml'
```Bash
pruning="custom"
pruning_keep_recent="1000"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.dymension/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.dymension/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.dymension/config/app.toml
```
#### Create a service file
```Bash
tee /etc/systemd/system/dymd.service > /dev/null <<EOF
[Unit]
Description=dymd
After=network-online.target

[Service]
User=$USER
ExecStart=$(which dymd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
#### Launch
```Bash
systemctl daemon-reload
systemctl enable dymd
systemctl restart dymd && journalctl -u dymd -f -o cat
```
### 3.Создание валидатора и генерация кошелька - Creating a validator and generating a wallet
#### Wallet 
```Bash
dymd keys add $WALLET --keyring-backend os

dymd keys add $WALLET --recover --keyring-backend os
```
Go to discord and use the tap 

#### Validator
```Bash
dymd tx staking create-validator \
--chain-id 35-C \
--commission-rate 0.05 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.1 \
--min-self-delegation "1000000" \
--amount 1000000udym \
--pubkey $(dymd tendermint show-validator) \
--moniker "<name_moniker>" \
--from <name_wallet> \
--fees 5000udym
```

### 4. Удаление - Delete
#### Deleting
```Shell
systemctl stop dymd && \
systemctl disable dymd && \
rm /etc/systemd/system/dymd.service && \
systemctl daemon-reload && \
cd $HOME && \
rm -rf .dymd dymd && \
rm -rf $(which dymd)
```
