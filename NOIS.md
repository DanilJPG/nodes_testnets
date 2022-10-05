#### Обновление и установка зависимостей 
```
apt-get update 
sudo apt install curl build-essential git wget jq make gcc tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```
#### Установка GO
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
#### Копирование репозитория
```
git clone https://github.com/noislabs/full-node.git 
cd full-node/full-node/
./setup.sh
mv out/noisd $HOME/go/bin/
export PATH=$HOME/go/bin:$PATH
```
#### Инициализация
```
noisd init <name_moniker> --chain-id nois-testnet-002
```
Замените '<name_moniker>' на название своего оператора
#### Скачиваем genesis
```
wget -qO $HOME/.noisd/config/genesis.json "https://raw.githubusercontent.com/noislabs/testnets/main/nois-testnet-002/genesis.json"
```
#### Правим конфиг 
```
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.noisd/config/config.toml

SEEDS=""
PEERS="a1222dfb8641e0cb55615b75e0122d5695be1f35@node-0.noisdlabs.com:26656,cf16671c00eec9a9a047a5c6aa8510cb681b64b8@node-3.noisdlabs.com:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.noisd/config/config.toml

noisd config chain-id nois-testnet-002
noisd config keyring-backend test
noisd config node tcp://localhost:${NOIS_PORT}657

sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${NOIS_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${NOIS_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${NOIS_PORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${NOIS_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${NOIS_PORT}660\"%" $HOME/.noisd/config/config.toml
sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${NOIS_PORT}317\"%; s%^address = \":8080\"%address = \":${NOIS_PORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${NOIS_PORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${NOIS_PORT}091\"%" $HOME/.noisd/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.noisd/config/config.toml
```

#### Сервисный файл
```
sudo tee /etc/systemd/system/noisd.service > /dev/null <<EOF
[Unit]
Description=noisd
After=network-online.target

[Service]
User=$USER
ExecStart=$(which noisd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
#### Запуск
```
sudo systemctl daemon-reload
sudo systemctl enable noisd
sudo systemctl restart noisd && sudo journalctl -u noisd -f -o cat
```
#### Создаем кошелек пока идет синхронизация 
```
# создать кошелек
noisd keys add <name_wallet> --keyring-backend os

# восстановить кошелек (после команды вставить seed)
noisd keys add <name_wallet> --recover --keyring-backend os
```
Идем в дискорд и пользуемся краном 
#### Валидатор
```
noisd tx staking create-validator \
--chain-id nois-testnet-002 \
--commission-rate 0.05 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.1 \
--min-self-delegation "1000000" \
--amount 1000000000000000000unois \
--pubkey $(noisd tendermint show-validator) \
--moniker "<name_moniker>" \
--from <name_wallet> \
--fees 5000unois
```
#### Полезные команды
```
# проверка высота блоков
noisd status 2>&1 | jq ."SyncInfo"."latest_block_height"

# проверить логи
journalctl -u noisd -f -o cat
journalctl --lines=100 --follow --unit noisd

# проверить статус синхронизации
curl localhost:26657/status

# проверить баланс
noisd q bank balances <address>

# вывести список кошельков
noisd keys list
```
