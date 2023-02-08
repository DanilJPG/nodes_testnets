## Babylon

[Discord](https://discord.gg/babylonchain) | [Docs](https://docs.babylonchain.io/docs/testnet/setup-node) | [Twitter](https://twitter.com/babylon_chain)
--- | --- | ---

Steps | Comments
--- | --- |
[Server Preparation](https://github.com/DanilJPG/nodes_testnets/blob/main/Babylon%20Chain/Readme.md#:~:text=1.%D0%9F%D0%BE%D0%B4%D0%B3%D0%BE%D1%82%D0%BE%D0%B2%D0%BA%D0%B0%20%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0%20%2D%20Server%20Preparation) | Check the version, if necessary update to the appropriate height blocks,server setup, Go language is necessary to work with a binary file and unpack it
[Working with a binary file and setting up](https://github.com/DanilJPG/nodes_testnets/blob/main/Babylon%20Chain/Readme.md#:~:text=.%D0%A0%D0%B0%D0%B1%D0%BE%D1%82%D0%B0%20%D1%81%20%D0%B1%D0%B8%D0%BD%D0%B0%D1%80%D0%BD%D1%8B%D0%BC%20%D1%84%D0%B0%D0%B9%D0%BB%D0%BE%D0%BC%20%D0%B8%20%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%2D%20Working%20with%20a%20binary%20file%20and%20setting%20up) | Cloning the GitHub repository of a project,To generate configuration files,The genesis stores the state of the chain,Making changes to config.toml,Creating a service file
[Creating a validator and generating a wallet]() | Creating and restoring a wallet,Creating your node operator
[Useful commands]() | Here are commands for the validator, for node management and for the wallet
[State Sunc]() | Chain synchronization
[Update]() | Stay tuned for the latest version

### 1.Подготовка сервера - Server Preparation 
#### Обновление и установка зависимостей - Upgrade and install dependencies
```Shell
sudo apt update && sudo apt upgrade -y && \
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```
#### Installing Go
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
```Shell
# Download the installation setup configuration
git clone https://github.com/babylonchain/babylon
cd babylon
git checkout v0.5.0
make install
```

#### Initializing 
```Shell
babylond init <name_moniker> --chain-id bbn-test-1
```

#### Download genesis
```Shell
wget https://github.com/babylonchain/networks/raw/main/bbn-test-1/genesis.tar.bz2
tar -xjf genesis.tar.bz2 && rm genesis.tar.bz2
mv genesis.json ~/.babylond/config/genesis.json
```

#### Correct the configuration file
```Shell
wget -O $HOME/.babylond/config/addrbook.json "https://share2.utsa.tech/babylon/addrbook.json"

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.babylond/config/config.toml

sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025ulava\"/;" ~/.babylond/config/app.toml

peers=""
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.babylond/config/config.toml

SEEDS="03ce5e1b5be3c9a81517d415f65378943996c864@18.207.168.204:26656,a5fabac19c732bf7d814cf22e7ffc23113dc9606@34.238.169.221:26656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.babylond/config/config.toml
```
#### Prunning `app.toml'
```Shell
pruning="custom"
pruning_keep_recent="1000"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.babylond/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.babylond/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.babylond/config/app.toml
```

#### Create a service file
```Shell
sudo tee /etc/systemd/system/babylond.service > /dev/null << EOF
[Unit]
Description=Babylon Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which babylond) start
Restart=on-failure
RestartSec=10
LimitNOFILE=10000
[Install]
WantedBy=multi-user.target
EOF
```

#### Launch
```Shell
sudo systemctl daemon-reload
sudo systemctl enable babylond
sudo systemctl start babylond

sudo journalctl -u babylond -f --no-hostname -o cat
```


### 3.Создание валидатора и генерация кошелька - Creating a validator and generating a wallet
#### Wallet 
```Shell
# создать кошелек
babylond  keys add <name_wallet> --keyring-backend os

# восстановить кошелек (после команды вставить seed)
babylond  keys add <name_wallet> --recover --keyring-backend os
```

#### Creating a validator 
```Shell
babylond tx staking create-validator \
--amount=9000000ubbn \
--pubkey=$(babylond tendermint show-validator) \
--moniker="$NODE_MONIKER" \
--chain-id=bbn-test1 \
--commission-rate=0.1 \
--commission-max-rate=0.2 \
--commission-max-change-rate=0.05 \
--min-self-delegation=1 \
--fees=10000ubbn \
--from=wallet \
-y
```

### 4. Удаление - Delete
#### Deleting
```Shell
systemctl stop babylond && \
systemctl disable babylond && \
rm /etc/systemd/system/babylond.service && \
systemctl daemon-reload && \
cd $HOME && \
rm -rf .babylond babylon && \
rm -rf $(which babylond)
```
