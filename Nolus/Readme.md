## Nolus

[Discord](https://discord.gg/nolus-protocol) | [Website](https://nolus.io/) | [Faucet](https://discord.com/channels/935531372334948383/1052895588527657020)
--- | --- | ---

Steps | Comments
--- | --- |
[Server Preparation](https://github.com/DanilJPG/nodes_testnets/tree/main/Nolus#:~:text=1.%D0%9F%D0%BE%D0%B4%D0%B3%D0%BE%D1%82%D0%BE%D0%B2%D0%BA%D0%B0%20%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0%20%2D%20Server%20Preparation) | Check the version, if necessary update to the appropriate height blocks,server setup, Go language is necessary to work with a binary file and unpack it
[Working with a binary file and setting up]() | Cloning the GitHub repository of a project,To generate configuration files,The genesis stores the state of the chain,Making changes to config.toml,Creating a service file
[Creating a validator and generating a wallet]() | Creating and restoring a wallet,Creating your node operator
[Useful commands]() | Here are commands for the validator, for node management and for the wallet
[Possible Errors]() | Here are possible errors related to the node and the validator


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
git clone https://github.com/Nolus-Protocol/nolus-core
cd nolus-core
git checkout v0.1.39
make install
```

#### Initializing
```Bash
nolusd init $NODENAME --chain-id nolus-rila && \
nolusd config chain-id altruistic-1
```

#### Download genesis
```Bash
cd $HOME/.nolus/config && wget $HOME/.nolus/config/genesis.json https://raw.githubusercontent.com/Nolus-Protocol/nolus-networks/main/testnet/nolus-rila/genesis.json
```
*Замените файл genesis по умолчанию в каталоге узла Nolus правильным файлом, который вы извлекли с помощью команды выше.*

*Override the default genesis file in your Nolus node directory with the correct one that you retrieved via the command above.*
```
mv ./genesis.json ~/.nolus/config/genesis.json
```
#### Correct the configuration file
```Shell
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.nolus/config/config.toml

sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025unls\"/;" ~/.nolus/config/app.toml

PEERS="$(curl -s "https://raw.githubusercontent.com/Nolus-Protocol/nolus-networks/main/testnet/nolus-rila/persistent_peers.txt")"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.nolus/config/config.toml

seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.nolus/config/config.toml

source ~/.profile
```
#### Prunning `app.toml'
```Bash
pruning="everything" && \
pruning_keep_recent="100" && \
pruning_keep_every="0" && \
pruning_interval="50" && \
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.nolus/config/app.toml && \
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.nolus/config/app.toml && \
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.nolus/config/app.toml && \
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.nolus/config/app.toml
```
#### Create a service file
```Bash
sudo tee /etc/systemd/system/nolusd.service > /dev/null <<EOF
[Unit]
Description=nolusd
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which nolusd) start
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
#### Launch
```Bash
systemctl daemon-reload && \
systemctl enable nolusd && \
systemctl restart nolusd && journalctl -u nolusd -f -o cat
```
### 3.Создание валидатора и генерация кошелька - Creating a validator and generating a wallet
#### Wallet 
```Bash
empowerd keys add $WALLET --keyring-backend os

empowerd keys add $WALLET --recover --keyring-backend os
```
Go to discord and use the tap 

#### Validator
```Bash
empowerd tx staking create-validator \
--chain-id altruistic-1 \
--commission-rate 0.05 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.1 \
--min-self-delegation "1000000" \
--amount 1000000umpwr \
--pubkey $(empowerd tendermint show-validator) \
--moniker "<name_moniker>" \
--from $WALLET \
--fees 5000umpwr
```

### 4. Удаление - Delete
#### Deleting
```Shell
systemctl stop empowerd && \
systemctl disable empowerd && \
rm /etc/systemd/system/empowerd.service && \
systemctl daemon-reload && \
cd $HOME && \
rm -rf .empowerchain empowerchain && \
rm -rf $(which empowerd)
```
