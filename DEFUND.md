### На данный момент времени 28.10 в 03-00 по МСК можно подать Gentx для отбора в актив сет
#### Обновляемся и устанавливаем зависимости
sudo apt update && sudo apt upgrade -y && \
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y

#### Установка Go
```
ver="1.19" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

#### Клонируем репозиторий 
```
git clone https://github.com/defund-labs/defund
cd defund
git checkout v0.1.0-alpha
make install
```

#### Инициализируем 
```
defundd init <moniker> --chain-id defund-private-2 \
defundd config chain-id defund-private-2
```

#### Скачиваем Genesis и Addrbook
```
wget -O /root/.defund/config//genesis.json "https://github.com/defund-labs/testnet/blob/main/defund-private-2/pre-genesis.json"
wget -O $HOME/.defund/config/addrbook.json https://raw.githubusercontent.com/sowell-owen/addrbooks/main/defund/addrbook.json
```
#### Правим конфиг
```
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" /root/.defund/config/config.toml

sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025ufetf\"/;" /root/.defund/config/app.toml

PEERS=``
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" ~/.lambdavm/config/config.toml

SEEDS=``
sed -i.bak -e "s/^seeds =.*/seeds = \"$SEEDS\"/" ~/.lambdavm/config/config.toml
```

#### Создаем сервисный файл
```
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
```
systemctl daemon-reload && \
systemctl enable defund && \
systemctl restart defund && journalctl -u defund -f -o cat
```
#### Wallet
```
# создать кошелек
defund keys add $WALLET --keyring-backend os

# восстановить кошелек (после команды вставить seed)
defund keys add $WALLET --recover --keyring-backend os
```
