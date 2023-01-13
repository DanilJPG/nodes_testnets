## Mars Protocol

[Discord](https://discord.gg/marsprotocol) | [Website](https://blog.marsprotocol.io/) | [Faucet](https://faucet.marsprotocol.io/)
| --- | --- | ---

Steps | Comments
--- | --- |
[Server Preparation](https://github.com/DanilJPG/nodes_testnets/tree/main/Mars#1%D0%BF%D0%BE%D0%B4%D0%B3%D0%BE%D1%82%D0%BE%D0%B2%D0%BA%D0%B0-%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0---server-preparation:~:text=1.%D0%9F%D0%BE%D0%B4%D0%B3%D0%BE%D1%82%D0%BE%D0%B2%D0%BA%D0%B0%20%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0%20%2D%20Server%20Preparation) | Check the version, if necessary update to the appropriate height blocks,server setup, Go language is necessary to work with a binary file and unpack it
[Working with a binary file and setting up](https://github.com/DanilJPG/nodes_testnets/blob/main/Mars/Readme.md#:~:text=2.%D0%A0%D0%B0%D0%B1%D0%BE%D1%82%D0%B0%20%D1%81%20%D0%B1%D0%B8%D0%BD%D0%B0%D1%80%D0%BD%D1%8B%D0%BC%20%D1%84%D0%B0%D0%B9%D0%BB%D0%BE%D0%BC%20%D0%B8%20%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%2D%20Working%20with%20a%20binary%20file%20and%20setting%20up) | Cloning the GitHub repository of a project,To generate configuration files,The genesis stores the state of the chain,Making changes to config.toml,Creating a service file
[Creating a validator and generating a wallet]() | Creating and restoring a wallet,Creating your node operator
[Useful commands]() | Here are commands for the validator, for node management and for the wallet
[Possible Errors]() | Here are possible errors related to the node and the validator
                                                             
### 1.Подготовка сервера - Server Preparation 
#### Обновление и установка зависимостей - Upgrade and install dependencies
```Shell
apt update && apt upgrade -y \

apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```

#### Go
```Shell
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
git clone https://github.com/mars-protocol/hub.git
cd hub
git checkou <...>
make install
```

#### Initializing
```Shell
marsd init garfield --chain-id ares-1

marsd config chain-id ares-1
```


#### Download genesis and address book
```Shell
curl -s https://testnet-rpc.marsprotocol.io/genesis | jq -r '.result.genesis' > $HOME/.mars/config/genesis.json
```


#### Correct the configuration file
```Shell
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.mars/config/config.toml

sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025umars\"/;" ~/.mars/config/app.toml

PEERS="6c855909a8bf1c12ef34baca059f5c0cdf82bc36@65.108.255.124:36656,e12bc490096d1b5f4026980f05a118c82e81df2a@85.17.6.142:26656w"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.mars/config/config.toml

seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.mars/config/config.toml
```


#### Create a service file
```Shell
sudo tee /etc/systemd/system/marsd.service > /dev/null <<EOF
[Unit]
Description=marsd
After=network-online.target

[Service]
User=$USER
ExecStart=$(which marsd) start
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
systemctl enable marsd
systemctl restart marsd && journalctl -u marsd -f -o catw
```

### 3.Создание валидатора и генерация кошелька - Creating a validator and generating a wallet
#### Wallet 
```Shell
# создать кошелек
marsd keys add <name_wallet> --keyring-backend os

# восстановить кошелек (после команды вставить seed)
marsd keys add <name_wallet> --recover --keyring-backend os
```


#### Creating a validator
```Shell
marsd tx staking create-validator \
--chain-id ares-1 \
--commission-rate 0.05 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.1 \
--min-self-delegation "1000000" \
--amount 1000000umars \
--pubkey $(nibid tendermint show-validator) \
--moniker "<name_moniker>" \
--from <name_wallet> \
--fees 5000umars
```

### 4. Удаление - Delete
#### Deleting
```Shell
systemctl stop marsd && \
systemctl disable marsd && \
rm /etc/systemd/system/mars.service && \
systemctl daemon-reload && \
cd $HOME && \
rm -rf .mars marsd && \
rm -rf $(which marsd)
```
