## Lava

[Discord](https://discord.gg/7H69AtB3) | [Website](https://lavanet.xyz/) | [Twitter](https://twitter.com/lavanetxyz)
--- | --- | ---

Steps | Comments
--- | --- |
[Server Preparation](https://github.com/DanilJPG/nodes_testnets/blob/main/Lava%20Network/Readme.md#:~:text=1.%D0%9F%D0%BE%D0%B4%D0%B3%D0%BE%D1%82%D0%BE%D0%B2%D0%BA%D0%B0%20%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0%20%2D%20Server%20Preparation) | Check the version, if necessary update to the appropriate height blocks,server setup, Go language is necessary to work with a binary file and unpack it
[Working with a binary file and setting up](https://github.com/DanilJPG/nodes_testnets/blob/main/Lava%20Network/Readme.md#:~:text=2.%D0%A0%D0%B0%D0%B1%D0%BE%D1%82%D0%B0%20%D1%81%20%D0%B1%D0%B8%D0%BD%D0%B0%D1%80%D0%BD%D1%8B%D0%BC%20%D1%84%D0%B0%D0%B9%D0%BB%D0%BE%D0%BC%20%D0%B8%20%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%2D%20Working%20with%20a%20binary%20file%20and%20setting%20up) | Cloning the GitHub repository of a project,To generate configuration files,The genesis stores the state of the chain,Making changes to config.toml,Creating a service file
[Creating a validator and generating a wallet]() | Creating and restoring a wallet,Creating your node operator
[Useful commands]() | Here are commands for the validator, for node management and for the wallet
[State Sunc]() | Chain synchronization

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
git clone https://github.com/K433QLtr6RA9ExEq/GHFkqmTzpdNLDd6T
wget https://lava-binary-upgrades.s3.amazonaws.com/testnet/v0.3.0/lavad
chmod +x lavad
mv lavad $HOME/go/bin/
```

#### Initializing 
```Shell
lavad init <name_moniker> --chain-id lava-testnet-1
lavad config chain-id lava-testnet-1
```

#### Download genesis
```Shell
cp $HOME/GHFkqmTzpdNLDd6T/testnet-1/genesis_json/genesis.json $HOME/.lava/config
#check

`sha256sum genesis.json`
# 1ff02001539bc1e9828fe170006f055c04df280c61c4ca9ecc9e7b6a272b7777  genesis.json
```

#### Correct the configuration file
```Shell
wget -O $HOME/.lava/config/addrbook.json "https://share2.utsa.tech/lava/addrbook.json"

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.lava/config/config.toml

sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025ulava\"/;" ~/.lava/config/app.toml

peers=""
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.lava/config/config.toml

seeds="3a445bfdbe2d0c8ee82461633aa3af31bc2b4dc0@prod-pnet-seed-node.lavanet.xyz:26656,e593c7a9ca61f5616119d6beb5bd8ef5dd28d62d@prod-pnet-seed-node2.lavanet.xyz:26656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.lava/config/config.toml
```
#### Prunning `app.toml'
```Shell
pruning="custom"
pruning_keep_recent="1000"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.lava/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.lava/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.lava/config/app.toml
```

#### Create a service file
```Shell
tee /etc/systemd/system/lavad.service > /dev/null <<EOF
[Unit]
Description=lavad
After=network-online.target

[Service]
User=$USER
ExecStart=$(which lavad) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

#### Launch
```Shell
systemctl daemon-reload
systemctl enable lavad
systemctl restart lavad && journalctl -u lavad -f -o cat
```


### 3.Создание валидатора и генерация кошелька - Creating a validator and generating a wallet
#### Wallet 
```Shell
# создать кошелек
lavad keys add <name_wallet> --keyring-backend os

# восстановить кошелек (после команды вставить seed)
lavad keys add <name_wallet> --recover --keyring-backend os

# восстановить кошелек для EVM сетей
lavad keys add <name_wallet> --recover --coin-type 118 --algo secp256k1
```

#### Creating a validator 
```Shell
lavad tx staking create-validator \
--chain-id lava-testnet-1 \
--commission-rate 0.05 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.1 \
--min-self-delegation "1000000" \
--amount 1000000ulava \
--pubkey $(lavad tendermint show-validator) \
--moniker "<name_moniker>" \
--from <name_wallet> \
--fees 5000ulava
```

### 4. Удаление - Delete
#### Deleting
```Shell
systemctl stop lavad && \
systemctl disable lavad && \
rm /etc/systemd/system/lavad.service && \
systemctl daemon-reload && \
cd $HOME && \
rm -rf .lava GHFkqmTzpdNLDd6T && \
rm -rf $(which lavad)
```
