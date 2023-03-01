## EmpowerChain


[Discord](https://discord.gg/KPtVcy3m) | [Website](https://empower.eco/) | [Faucet](https://discord.gg/T5N4V5qd)
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
git clone https://github.com/andromedaprotocol/andromedad.git
cd andromedad
git checkout galileo-3-v1.1.0-beta1
make install
andromedad version
```
#### Initializing
```Bash
andromedad config keyring-backend test
andromedad config chain-id galileo-3
andromedad init "$NODE_MONIKER" --chain-id galileo-3
```
#### Download genesis and addrbook
```Bash
curl -s https://raw.githubusercontent.com/andromedaprotocol/testnets/galileo-3/genesis.json > $HOME/.andromedad/config/genesis.json
curl -s https://snapshots2-testnet.nodejumper.io/andromeda-testnet/addrbook.json > $HOME/.andromedad/config/addrbook.json
```
#### Correct the configuration file
```Shell
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.andromedad/config/config.toml

sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025umpwr\"/;" ~/.andromedad/config/app.toml

SEEDS=""
PEERS=""
sed -i 's|^seeds *=.*|seeds = "'$SEEDS'"|; s|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.andromedad/config/config.toml
```
#### Prunning `app.toml'
```Bash
sed -i 's|^pruning *=.*|pruning = "custom"|g' $HOME/.andromedad/config/app.toml
sed -i 's|^pruning-keep-recent  *=.*|pruning-keep-recent = "100"|g' $HOME/.andromedad/config/app.toml
sed -i 's|^pruning-interval *=.*|pruning-interval = "10"|g' $HOME/.andromedad/config/app.toml
sed -i 's|^snapshot-interval *=.*|snapshot-interval = 2000|g' $HOME/.andromedad/config/app.toml

sed -i 's|^minimum-gas-prices *=.*|minimum-gas-prices = "0.0001uandr"|g' $HOME/.andromedad/config/app.toml
sed -i 's|^prometheus *=.*|prometheus = true|' $HOME/.andromedad/config/config.toml
```
#### Create a service file
```Bash
sudo tee /etc/systemd/system/andromedad.service > /dev/null << EOF
[Unit]
Description=Andromeda testnet Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which andromedad) start
Restart=on-failure
RestartSec=10
LimitNOFILE=10000
[Install]
WantedBy=multi-user.target
EOF
```
#### Launch
```Bash
sudo systemctl daemon-reload
sudo systemctl enable andromedad
sudo systemctl restart andromedad

sudo journalctl -u andromedad -f --no-hostname -o cat
```
### 3.Создание валидатора и генерация кошелька - Creating a validator and generating a wallet
#### Wallet 
```Bash
andromedad keys add $WALLET --keyring-backend os

andromedad keys add $WALLET --recover --keyring-backend os
```
Go to discord and use the tap 

#### Validator
```Bash
andromedad tx staking create-validator \
--amount=1000000uandr \
--pubkey=$(andromedad tendermint show-validator) \
--moniker="$NODE_MONIKER" \
--chain-id=galileo-3 \
--commission-rate=0.1 \
--commission-max-rate=0.2 \
--commission-max-change-rate=0.05 \
--min-self-delegation=1 \
--fees=10000uandr \
--from=wallet
```

### 4. Удаление - Delete
#### Deleting
```Shell
systemctl stop andromedad && \
systemctl disable andromedad && \
rm /etc/systemd/system/andromedad.service && \
systemctl daemon-reload && \
cd $HOME && \
rm -rf .andromedad andromedad && \
rm -rf $(which andromedad)
```
