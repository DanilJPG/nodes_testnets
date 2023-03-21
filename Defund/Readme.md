## Defund

#### Обновляемся и устанавливаем зависимости
```Shell
sudo apt update && sudo apt upgrade -y && \
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```
#### Установка Go
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

#### Клонируем репозиторий 
```Shell
cd $HOME
rm -rf defund
git clone https://github.com/defund-labs/defund.git
cd defund
git checkout v0.2.6
make build
```

#### Инициализируем 
```Shell
defundd init <moniker> --chain-id orbit-alpha-1 \
defundd config chain-id orbit-alpha-1
```

#### Скачиваем Genesis и Addrbook
```Shell
curl -Ls https://snapshots.kjnodes.com/defund-testnet/genesis.json > $HOME/.defund/config/genesis.json
curl -Ls https://snapshots.kjnodes.com/defund-testnet/addrbook.json > $HOME/.defund/config/addrbook.json
```
#### Правим конфиг
```Shell
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" /root/.defund/config/config.toml

sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025ufetf\"/;" /root/.defund/config/app.toml

PEERS=``
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" ~/.defund/config/config.toml

SEEDS=``
sed -i.bak -e "s/^seeds =.*/seeds = \"$SEEDS\"/" ~/.defund/config/config.toml
```

#### Создаем сервисный файл
```Shell
sudo tee /etc/systemd/system/defund.service > /dev/null <<EOF
[Unit]
Description=defund
After=network-online.target

[Service]
User=$USER
ExecStart=$(which defund) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
#### Запуск
```Shell
systemctl daemon-reload && \
systemctl enable defund && \
systemctl restart defundd && journalctl -u defundd -f -o cat
```
#### Wallet
```Shell
# создать кошелек
defund keys add <name_wallet> --keyring-backend os

# восстановить кошелек (после команды вставить seed)
defund keys add <name_wallet> --recover --keyring-backend os
```

#### Validator
```Shell
defund tx staking create-validator \
--amount 1000000ufetf \
--pubkey $(defund tendermint show-validator) \
--moniker "<moniker>" \
--chain-id defund-private-2 \
--commission-rate "0.05" \
--min-self-delegation "1000000" \
--commission-max-rate "0.20" \
--commission-max-change-rate "0.01" \
--from garfield_wallet \
--gas=auto \
--identity "your keybase" \ 
--details "Your validator motto" \
--fees 5000ufetf
```

#### Delete 
 ```Shell
sudo systemctl stop defund && \
sudo systemctl disable defund && \
rm /etc/systemd/system/defund.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf .defund defund && \
rm -rf $(which defund)
 ```
 
#### Change ports
```
# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:36658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:16657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:5061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:16656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":16660\"%" $HOME/.defund/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:9190\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:9291\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1327\"%" $HOME/.defund/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:36657\"%" $HOME/.defund/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:36656\"/" $HOME/.defund/config/config.toml
```
