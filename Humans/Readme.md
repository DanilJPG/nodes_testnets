## Humans

[Discord](https://discord.gg/humansdotai) | [Docs](https://docs.humans.zone/) | [Twitter](https://twitter.com/humansdotai)
--- | --- | ---

Steps | Comments
--- | --- |
[Server Preparation](https://github.com/DanilJPG/nodes_testnets/blob/main/Humans/Readme.md#:~:text=1.%D0%9F%D0%BE%D0%B4%D0%B3%D0%BE%D1%82%D0%BE%D0%B2%D0%BA%D0%B0%20%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0%20%2D%20Server%20Preparation) | Check the version, if necessary update to the appropriate height blocks,server setup, Go language is necessary to work with a binary file and unpack it
[Working with a binary file and setting up](https://github.com/DanilJPG/nodes_testnets/blob/main/Babylon%20Chain/Readme.md#:~:text=.%D0%A0%D0%B0%D0%B1%D0%BE%D1%82%D0%B0%20%D1%81%20%D0%B1%D0%B8%D0%BD%D0%B0%D1%80%D0%BD%D1%8B%D0%BC%20%D1%84%D0%B0%D0%B9%D0%BB%D0%BE%D0%BC%20%D0%B8%20%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%2D%20Working%20with%20a%20binary%20file%20and%20setting%20up) | Cloning the GitHub repository of a project,To generate configuration files,The genesis stores the state of the chain,Making changes to config.toml,Creating a service file
[Creating a validator and generating a wallet](https://github.com/DanilJPG/nodes_testnets/blob/main/Babylon%20Chain/Readme.md#:~:text=3.%D0%A1%D0%BE%D0%B7%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5%20%D0%B2%D0%B0%D0%BB%D0%B8%D0%B4%D0%B0%D1%82%D0%BE%D1%80%D0%B0%20%D0%B8%20%D0%B3%D0%B5%D0%BD%D0%B5%D1%80%D0%B0%D1%86%D0%B8%D1%8F%20%D0%BA%D0%BE%D1%88%D0%B5%D0%BB%D1%8C%D0%BA%D0%B0%20%2D%20Creating%20a%20validator%20and%20generating%20a%20wallet) | Creating and restoring a wallet,Creating your node operator
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
git clone https://github.com/humansdotai/humans
cd humans
git checkout v1.0.0
go build -o humansd cmd/humansd/main.go
sudo cp humansd /usr/local/bin/humansd
```

#### Initializing 
```Shell
humansd init <name_moniker> --chain-id testnet-1
humansd config keyring-backend test
humansd config chain-id testnet-1
```

#### Download genesis
```Shell
curl -s https://rpc-testnet.humans.zone/genesis | jq -r .result.genesis > $HOME/.humans/config/genesis.json
```

#### Correct the configuration file
```Shell
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.humans/config/config.toml

sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025uheart\"/;" ~/.humans/config/app.toml

PEERS="e4234a5fba85b2c3d2ad157b6961ac3d115f4c49@humans-testnet.nodejumper.io:28656,fceabdb52e28e110b0c2e695b7295bd14af65f1f@195.201.59.194:26656,d5daa4f7089019fb845f18b0dd9ac9b47c3afe93@23.88.71.247:30656,d5e444638a236c6cf3e09167224f48b2f77a6611@185.198.27.109:2556,5c27e54b2b8a597cbbd1c43905d2c18a67637644@142.132.231.118:56656,dee3d7cbbb1ac884c008fefab23d53dc9d96b846@185.219.142.182:26656,e1fc3fd90808ff158102ef003ef7b6f056d7e27d@185.16.39.19:26656,198b1c1f136e5d24f33c218a027dd6394dab74ab@135.181.82.28:26656,636648f03fdda72d7caec67fcbf5e20a8a53d590@109.123.244.178:26656,01abf63cc2b799bb53d4d1a8c12f8713737e84ca@157.245.52.27:26656,17f4b40a52cb18293edc4f3c13e33efd09f446d4@65.109.53.60:26656,f88a461adb2db0ffdc9fed8d3caab08a4b327ce7@65.108.231.124:17656,327d518a106ac960f1d5ea78c228c244f0942562@82.65.197.168:26656,2f33b1312afcfffabae9f417bba0d29fe05f609d@65.108.78.101:26656,412888b64c840b879e34bd080dc233603bdd04b6@85.173.113.198:23656,049d4807acc00a42ed64a57b5f58c1c89d5be9db@65.109.88.180:15656,c2cf4e1d0da9ac1e8be5d5288d0bf8e8052b2d86@65.109.92.148:60856,4853c63022259d8c9f64c73353600249d905d227@212.90.121.121:13656,3f13ad6e8795479b051d147a5049bf4bd0a63817@65.109.87.88:22656,e0d59d2c5058552f536f4d21227f6d1050a16d57@65.109.106.91:22656,67826eb77eab91711f938132a9416dd1ec615b1b@65.109.88.254:39656,76c37181ddb27a9917a465d27be248891d85425d@162.248.224.186:26656,f9b186dffae34134d108e215b8d471c22f9f5b02@195.3.222.188:26656,dc4d6e5bc6a6a75f177d4d59ad584f9fbd3eb009@104.248.232.113:13656,d62cc03a547509ff40d7496c35471c3d640b9f61@34.68.218.99:26656,69822c67487d4907f162fdd6d42549e1df60c82d@65.21.224.248:26656,e8c875d2462e66ed5ee2671df4ba310cc9f8a4bf@95.214.55.62:60556,6aab8fbe8d8b8b61a17976f3b154282bec3a2d6c@176.9.22.117:12656,df698e4ff0e45324d67d312581574be8f3c1c4f1@144.76.27.79:46656,70adc2b27a27c69757d7399a21e1e80ee2703d94@65.109.84.215:60856,16e6bdd012b108e2a6ebe5fb26a31d0157238850@104.248.240.13:26656,f0a662bb16f6734f96c287d7012d8b004dc24c67@65.109.92.235:11026,96622dab2bebab9ff2ae2720feac5866215ad5b7@104.248.254.182:26656,28cee93eee4b0b800b362f8bba5a3edd25ff1030@195.201.83.166:48656,33f0ebee09c9420fbc56c61548eab66c5ebdbeb5@91.223.3.144:26756,c5d5a7b399867350c393f76988e2126012f2e064@75.119.133.212:26656,96fc064917274a80d43985a5c3440254dcae5dc9@65.108.134.208:36656,aec858a71cd3a57f7da8bcd5e80eca17d269af21@159.223.212.84:17656,739c605c870d8ef83a2e168fbaa77d6acfbe0de3@65.21.129.95:26656,184d6a0b185e263245810f6b8778aad49741c074@213.136.90.117:26656,c692c561c78549f4cfa8be220913189d5e35da30@164.90.221.176:26656,9d72348318e67750c9bb1e2a12c6a53fae7911eb@75.119.130.88:26656,a4f9fd8d76dd3fb4fc72b174be1e3bd6590a4d53@45.147.176.14:26656,54ca3e14e71fefb83ada327bcab7eed603907af3@65.109.165.99:26656,1e32e98f500f95ffde43236ec84153a051621130@15.235.80.84:26656,2685f8e77fec93c99a55f2adb13835a50124d04e@135.181.18.112:55686,958509db695a02e9cf514bb99793051bea11af45@65.109.88.251:11026,e42caa91e00da31258aa1b7b9a9e5d64062d6739@167.172.72.136:26656,f981952b0d46439b7bc1de9053865f72bcf662df@157.230.240.157:26656,f3d94eb33bad79e57af24743cea52cb3fbbbf45c@65.109.70.23:18456,7b0b40f045e66d83760859f42e8e95ce7ad93409@88.99.164.158:1166"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.humans/config/config.toml

SEEDS=""
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
sudo tee /etc/systemd/system/humansd.service > /dev/null << EOF
[Unit]
Description=Humans AI Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which humansd) start
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
sudo systemctl enable humansd
sudo systemctl start humansd

sudo journalctl -u humansd -f --no-hostname -o cat
```


### 3.Создание валидатора и генерация кошелька - Creating a validator and generating a wallet
#### Wallet 
```Shell
# создать кошелек
humansd  keys add <name_wallet> --keyring-backend os

# восстановить кошелек (после команды вставить seed)
humansd  keys add <name_wallet> --recover --keyring-backend os
```

#### Creating a validator 
```Shell
humansd tx staking create-validator \
--amount=9000000uheart \
--pubkey=$(humansd tendermint show-validator) \
--moniker="$NODE_MONIKER" \
--chain-id=testnet-1 \
--commission-rate=0.1 \
--commission-max-rate=0.2 \
--commission-max-change-rate=0.05 \
--min-self-delegation=1 \
--fees=10000uheart \
--from=wallet \
-y
```

### 4. Удаление - Delete
#### Deleting
```Shell
systemctl stop humansd && \
systemctl disable humansd && \
rm /etc/systemd/system/humansd.service && \
systemctl daemon-reload && \
cd $HOME && \
rm -rf .humans humans && \
rm -rf $(which humansd)
