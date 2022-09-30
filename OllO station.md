#### Обновляемся
```
apt update && apt upgrade -y
```

#### Устанавливаем зависимости
```
sudo apt-get install curl git make gcc liblz4-tool build-essential jq -y
```

#### Установка GO
```
ver="1.18.1" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

#### Клонируем репозитория 
```
git clone https://github.com/OLLO-Station/ollo
cd ollo
make install
```

#### Инициализируем
```
ollod init --chain-id "ollo-testnet-0" "garfield"
```
```
sha256sum ~/.ollo/config/genesis.json
257123e2c824fba30786a3db6c0f4ff22ea63ec7bcaea7db47c0d333c0cbcaf5  /root/.ollo/config/genesis.json
```
#### Создаем кошелек
```
ollod keys add <name_wallet> --keyring-backend os
ollod keys add <name_wallet> --recover --keyring-backend os
```
#### Check wallet
```
ollod keys list
```
#### Скачиваем генезис
```
wget -qO $HOME/.ollo/config/genesis.json https://github.com/AlexToTheMoon/AM-Solutions/raw/main/ollo-genesis.json
```
Скачиваем адресбук
```
wget $HOME/.ollod/config/addrbook.json https://raw.githubusercontent.com/OllO-Station/ollo/master/networks/ollo-testnet-0/addrbook.json
```
#### Настраиваем кофинг файл
```
sed -i '/\[api\]/,+3 s/enable = false/enable = true/' ~/.ollo/config/app.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.ollod/config/config.toml

sed -i '/\[statesync\]/,+3 s/enable = false/enable = true/' ~/.ollo/config/config.toml

peers="1be12d239ca70a906264de0f09e0ffa01c4211ba@138.201.136.49:26656,06658ccd5c119578fb662633234a2ef154881b94@18.144.61.148:26656,a77c2afc500569a453b7fb64c8a804878dc6e7be@65.108.127.215:26856,2eeb90b696ba9a62a8ad9561f39c1b75473515eb@77.37.176.99:26656,eaee85418b4fc3e7e2e298bb8deb5a8f49956859@5.9.13.234:26856,45acf9ea2f2d6a2a4b564ae53ea3004f902d3fb7@185.182.184.200:26656,62b5364abdfb7c0934afaddbd0704acf82127383@65.108.13.185:27060,e2b22ed4b00f37adafed6d711432f612821f5943@77.52.182.194:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.ollo/config/config.toml

seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.ollo/config/config.toml
```

#### Создаем сервисный файл
```
sudo tee /etc/systemd/system/ollod.service > /dev/null <<EOF
[Unit]
Description=ollod
After=network-online.target

[Service]
User=$USER
ExecStart=$(which ollod) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535
[Install]

WantedBy=multi-user.target
EOF           
```

#### Запуск
```
sudo systemctl daemon-reload && \
sudo systemctl enable ollod && \
sudo systemctl restart ollod && sudo journalctl -u ollod -f -o cat
```
#### Проверим логи
```
journalctl -fu ollod -o cat
```
#### STATE SYNC 
```
sudo systemctl stop ollod

SNAP_RPC="http://161.97.82.203:29987"; \
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash); \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.ollo/config/config.toml

wget -qO $HOME/.ollo/config/addrbook.json https://github.com/AlexToTheMoon/AM-Solutions/raw/main/ollo-addrbook.json
ollod tendermint unsafe-reset-all --home $HOME/.ollo --keep-addr-book

sudo systemctl restart ollod
journalctl -u ollod -f -o cat

#switch off state sync after sync
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1false|" $HOME/.ollo/config/config.toml
```
 

#### CREATE VALIDATOR
```
 ollod tx staking create-validator \
 --amount=1000000utollo \
 --pubkey=$(ollod tendermint show-validator) \
 --moniker="<MONIKER>" \
 --chain-id="<CHAIN-ID>" \
 --from=<name_wallet> \
 --commission-rate=0.05 \
 --commission-max-rate=0.2 \
 --commission-max-change-rate=0.05 \
 --min-self-delegation=1 \
--gas=auto
 ```
#### Полезные команды
```
# проверить блоки
ollod status 2>&1 | jq ."SyncInfo"."latest_block_height"

# проверить логи
journalctl -u ollod -f -o cat
journalctl --lines=100 --follow --unit ollod

# проверить статус
curl localhost:26657/status

# проверить баланс
ollod q bank balances <address>
```
