## STRIDE 

## Babylon

[Discord](https://discord.gg/stride-988945059783278602) | [Twitter](https://twitter.com/babylon_chain)
--- | --- | 

Steps | Comments
--- | --- |
[Server Preparation]() | Check the version, if necessary update to the appropriate height blocks,server setup, Go language is necessary to work with a binary file and unpack it
[Working with a binary file and setting up]() | Cloning the GitHub repository of a project,To generate configuration files,The genesis stores the state of the chain,Making changes to config.toml,Creating a service file
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
git clone https://github.com/Stride-Labs/stride.git
cd stride
git checkout v8.0.0
make install
```

#### Initializing 
```Shell
strided config chain-id stride-1
strided init "$NODE_MONIKER" --chain-id stride-1
```

#### Download genesis
```Shell
curl -s https://raw.githubusercontent.com/Stride-Labs/stride/main/genesis/genesis.json > $HOME/.stride/config/genesis.json
curl -s https://snapshots1.nodejumper.io/stride/addrbook.json > $HOME/.stride/config/addrbook.json
```

#### Correct the configuration file
```Shell
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.stride/config/config.toml

sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025ubbn\"/;" ~/.stride/config/app.toml

SEEDS=""
PEERS="de048f413e6cceeba02139f581adb97d331651b8@stride.nodejumper.io:29656,a2128f5552cf4ae60a769999c7fddc5d9d44d149@15.235.42.151:26661,04b797b5a56fb939a97a3c7d9c3230d09b85e8d7@93.189.30.118:26656,c3467e5becb108e62f6a6051eb5551e9f256d096@174.83.6.129:26656,cc35475fe1f7c345af0ea8a692f3b4b41c8f12a2@116.202.36.240:10156,44e797771bff124693e63a8ec331d42873cf2ae2@95.217.202.49:35656,b212d5740b2e11e54f56b072dc13b6134650cfb5@164.152.160.97:26656,e2edd2fcc4b165374eafe381e54b6787ebc5bd67@162.55.245.149:2030,2254e6968e5c7ebc98ef5b79b388502fa44e10e1@5.161.134.44:26656,9ed4a1c80960ae933551283eb8aef52468f6cfc7@65.109.106.169:26656,463b1dc6903455575079572fb23407be586f2a4b@185.16.39.37:26656,fc305427390397f8c4eebe5bc22919c1cc5d4532@65.109.43.75:27007,ade7d4d0009c7725ee991b8c40a7f646f76bf1e3@149.102.140.108:26656,89757803f40da51678451735445ad40d5b15e059@169.155.168.67:26656,05eec003db41d7ff47a317ef59f83e31bdca23c3@78.107.234.44:26656,8a210f1bcfc9015a7bc18dcc5add29c0dce3f2dc@135.181.173.67:26656,8e4e1f1e087c76c71c64e477e95495833da82aa2@135.181.173.139:26656,28ca5fc2464e9494e8d5bd93955cde707e4e208e@34.29.90.236:26656,3fef899adcdeded56f6c69fe55c5da1624303367@163.172.101.208:4656,9ee75491e354965d8bfd8434aa093f8613bc1dce@65.108.238.103:12256,54672e848a31d2e7aeda35b8f2c320ad508c5550@128.199.141.132:26656,5093547fdf0430143ac66b4ee55d80e6542a6c10@217.174.247.163:26656,a77173bc4f4171fec0ac56b37c18e0ba6e5f80a4@65.108.226.44:31656,2c1f55e905c7425f995947e2d600ca5ac863b8c1@15.235.53.91:13456,51b83e27aee30e1900539cef37f18bddd4eab2d9@51.77.57.29:6000,abaf98731ce081fa2f32da7db0ff27b1db1c1c99@80.64.208.151:26656,cd680cc992983e5c8244b5529034a2e362e7a6d3@93.159.134.157:26656,82588f011491c6100d922d133f52fc23460b9231@135.181.67.235:26656,6831d67983cf5ebcb44da01737ccd6ccbd15c08e@193.70.47.90:12256,cb6ae22e1e89d029c55f2cb400b0caa19cbe5523@172.111.52.51:32661,446d388856dde233a206d8649fdc24efbde2b57c@34.71.33.155:26656,b549e0f88cbebe6cfd3f772937a70640b950fd98@66.172.36.133:28656,8fff37214fb0ef622f1c09dccb22d6321e004c3e@109.123.242.163:50056,9731c3365c772b3bc4580de5708a33f22c6174ec@208.102.87.76:26656,450d000d0d5c010cb2e7c45b72e6cda08a22fd04@35.224.198.112:26656,d36ac7580cc8907a00b0add8c3b047caea6df4ed@107.155.67.202:26636,7ab3bfcdbe618ed62317cbc40ef48aee783fb2b4@144.76.152.68:4656,1483ddbd1ba369c01d5496877314ed1b09bd9cc3@65.21.189.221:12256,6856de6f0c70a850db2b58deb43d568fced4a524@165.227.208.6:26656,ea6a7b2f366bc343f0670f1673fd86001dd08eb0@65.108.122.246:26636,0198f6d3ebe7bed4d176558a2ce8d341531f3e7b@74.80.183.130:26653,c7a30393c5cab01f5b497c4c094424e4e6271bac@65.108.201.154:5010,c4688bb34164eacacaa374bc7440b87986dd87ac@162.251.235.252:26656,978477aab55c2494ad486477f0793f21a83c937f@34.173.31.167:26656,cb0b38aa612e8ac05f704d9b2feb7526607afb77@66.94.117.176:26656,ebc272824924ea1a27ea3183dd0b9ba713494f83@185.16.39.158:26886,8ade90b45b991088c92e8583e8bc93589d6cd81e@84.244.95.247:26656,5383a21cf2d5e513aea2c3e430133f31aa2e5d00@138.201.32.103:26656,718ce477a62a14efe61571bd836fd3db9e43e6c1@38.105.232.61:26656,0393c19b176d1cf8bc560c5a8fa990301deb1a7e@95.217.126.187:26656,a1f479dc2e3322c6547a39c6c7eef5a191def57f@34.66.206.221:26656"
sed -i 's|^seeds *=.*|seeds = "'$SEEDS'"|; s|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.stride/config/config.toml

```
#### Prunning `app.toml'
```Shell
sed -i 's|^pruning *=.*|pruning = "custom"|g' $HOME/.stride/config/app.toml
sed -i 's|^pruning-keep-recent  *=.*|pruning-keep-recent = "100"|g' $HOME/.stride/config/app.toml
sed -i 's|^pruning-interval *=.*|pruning-interval = "10"|g' $HOME/.stride/config/app.toml
sed -i 's|^snapshot-interval *=.*|snapshot-interval = 0|g' $HOME/.stride/config/app.toml

sed -i 's|^minimum-gas-prices *=.*|minimum-gas-prices = "0.0001ustrd"|g' $HOME/.stride/config/app.toml
sed -i 's|^prometheus *=.*|prometheus = true|' $HOME/.stride/config/config.toml
```

#### Create a service file
```Shell
sudo tee /etc/systemd/system/strided.service > /dev/null << EOF
[Unit]
Description=Stride Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which strided) start
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
sudo systemctl enable strided
sudo systemctl start strided

sudo journalctl -u strided -f --no-hostname -o cat
```


### 3.Создание валидатора и генерация кошелька - Creating a validator and generating a wallet
#### Wallet 
```Shell
# создать кошелек
strided  keys add <name_wallet> --keyring-backend os

# восстановить кошелек (после команды вставить seed)
strided  keys add <name_wallet> --recover --keyring-backend os
```

#### Creating a validator 
```Shell
strided tx staking create-validator \
--amount=1000000ustrd \
--pubkey=$(strided tendermint show-validator) \
--moniker="Moniker" \
--identity=FFB0AA51A2DF5955 \
--details="I'm sexy and I know it😉" \
--chain-id=stride-1 \
--commission-rate=0.10 \
--commission-max-rate=0.20 \
--commission-max-change-rate=0.01 \
--min-self-delegation=1 \
--from=wallet \
--gas-prices=0.1ustrd \
--gas-adjustment=1.5 \
--gas=auto \
-y 
```

### 4. Удаление - Delete
#### Deleting
```Shell
systemctl stop strided && \
systemctl disable strided && \
rm /etc/systemd/system/strided.service && \
systemctl daemon-reload && \
cd $HOME && \
rm -rf .strided stride && \
rm -rf $(which strided)
```
