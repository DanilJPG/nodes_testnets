## STRIDE 

Discord: https://discord.gg/stride-zone

Website - https://stride.zone/

Twitter - https://twitter.com/stride_zone
#### Обновимся и установим зависимости
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl build-essential git wget jq make gcc tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```
#### Установка Go:
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
#### Установка бинарного файла
```
git clone https://github.com/Stride-Labs/stride && cd stride
git checkout cf4e7f2d4ffe2002997428dbb1c530614b85df1b
#если не создана папка $HOME/go/bin, то создаем ее
mkdir -p $HOME/go/bin
go build -mod=readonly -trimpath -o $HOME/go/bin ./...
```
#### Инициализация ноды
```
strided init <name_moniker> --chain-id STRIDE-TESTNET-4
```
#### Обновление Genesis
```
wget -qO $HOME/.stride/config/genesis.json "https://raw.githubusercontent.com/Stride-Labs/testnet/main/poolparty/genesis.json"
```
#### Обновление seeds/peers:
```
SEEDS="d2ec8f968e7977311965c1dbef21647369327a29@seedv2.poolparty.stridenet.co:26656"
PEERS="2771ec2eeac9224058d8075b21ad045711fe0ef0@34.135.129.186:26656,a3afae256ad780f873f85a0c377da5c8e9c28cb2@54.219.207.30:26656,328d459d21f82c759dda88b97ad56835c949d433@78.47.222.208:26639,bf57701e5e8a19c40a5135405d6757e5f0f9e6a3@143.244.186.222:16656,f93ce5616f45d6c20d061302519a5c2420e3475d@135.125.5.31:54356"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.stride/config/config.toml
```
#### Обновляем конфиг файл
```
sed -i '/STRIDE_CHAIN_ID/d' ~/.bash_profile
echo "export STRIDE_CHAIN_ID=STRIDE-TESTNET-4" >> $HOME/.bash_profile
source $HOME/.bash_profile
strided config chain-id $STRIDE_CHAIN_ID
```
#### Сбрасываем данные цепочки и отключаем синхронизацию состояний
```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1false|" $HOME/.stride/config/config.toml
strided tendermint unsafe-reset-all --home $HOME/.stride
```
#### Запускаем сервисный файл
```
sudo systemctl start strided && journalctl -fu strided -o cat
```
#### Проверяем баланс кошелька
```
strided query bank balances $STRIDE_WALLET_ADDRESS
```
#### Создаем валидатора
```
strided tx staking create-validator \
  --amount 10000000ustrd \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(strided tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $STRIDE_CHAIN_ID
```
#### Делегируем нашему валидатору
```
strided tx staking delegate $STRIDE_VALOPER_ADDRESS <AMOUNT>ustrd --from=$WALLET --chain-id=$STRIDE_CHAIN_ID --gas=auto
```
#### Устанавливаем бинарный файл
```
NODE_MONIKER=<YOUR_NODE_MONIKER>
```
```
cd || return
rm -rf stride
git clone https://github.com/Stride-Labs/stride.git
cd stride || return
git checkout 4ec1b0ca818561cef04f8e6df84069b14399590e
make build
mkdir -p $HOME/go/bin
sudo cp $HOME/stride/build/strided $HOME/go/bin
strided version #
```
#### Инициализируем ноду, чтобы создать необходимые файлы конфигурации
```
меняем название <name_moniker> вашей ноды на то, которое хотите

strided init $NODE_MONIKER --chain-id STRIDE-TESTNET-2
```
#### Скачиваем Genesis
```
wget -O $HOME/.stride/config/genesis.json "https://raw.githubusercontent.com/Stride-Labs/testnet/main/poolparty/genesis.json"
```
#### Скачиваем addrbook
```
wget -O $HOME/.stride/config/addrbook.json "https://raw.githubusercontent.com/StakeTake/guidecosmos/main/stride/STRIDE-TESTNET-2/addrbook.json"
```
#### Правим конфиг файл
```
strided config chain-id STRIDE-TESTNET-2
```
#### Добавляем peers и seeds, настраиваем сбор газа
```
sed -i 's|^minimum-gas-prices *=.*|minimum-gas-prices = "0.0001ustrd"|g' $HOME/.stride/config/app.toml

# добавляем seeds/peers в config.toml
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.stride/config/config.toml

seeds="c0b278cbfb15674e1949e7e5ae51627cb2a2d0a9@seedv2.poolparty.stridenet.co:26656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.stride/config/config.toml

peers="17b24705533d633cb3501233a18912ae6cc36a41@stride-testnet.nodejumper.io:28656,48b1310bc81deea3eb44173c5c26873c23565d33@stride-testnet-2-node1.poolparty.stridenet.co:26656,17b24705533d633cb3501233a18912ae6cc36a41@stride-testnet.nodejumper.io:28656,fe32bb1711c38fd5af5ec9ae9db50c694c872194@23.88.77.188:20005,04ef750b843220e6aa93a1b097a4e51e5257a910@135.181.19.231:36656,57e3541c58441e2f6f955eb67b125b09a96086ac@65.21.131.215:26616,d5a52e246601ea09f4543e43aab65fea113ce080@65.108.101.50:60556,a4c25f4b10e33c72749861ed9e142e2986aaaeec@213.239.218.210:26656,8c069ced6c1689c5680efa8f9b26df20b83bcd4d@141.147.7.244:16656,fef5a04c72fb967e4271d0d73cfa8a87234b0dd3@95.217.155.89:16656,b96c807bfcab89fbf50c1d333701bd7ef255f7d6@65.108.58.240:16656,6ccb1b48b804571738abd1c5b376e5fcddc9a1e5@194.163.162.179:26656,c283ee6d2753e432f1103677b9db98a5a66356f6@146.190.26.115:16656,43dac7973942595885bb7930382e4514be19504b@31.187.74.204:16656,469d045599d0812a8a3b1e3e36358b7f8e4e2d53@95.216.218.102:26656"
sed -i -e 's|^seeds *=.*|seeds = "'$seeds'"|; s|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.stride/config/config.toml

Pruning (одной командой):

sed -i 's|pruning = "default"|pruning = "custom"|g' $HOME/.stride/config/app.toml
sed -i 's|pruning-keep-recent = "0"|pruning-keep-recent = "100"|g' $HOME/.stride/config/app.toml
sed -i 's|pruning-interval = "0"|pruning-interval = "10"|g' $HOME/.stride/config/app.toml
sed -i 's|^snapshot-interval *=.*|snapshot-interval = 0|g' $HOME/.stride/config/app.toml
```
#### Создаем сервисный файл
```
sudo tee /etc/systemd/system/strided.service > /dev/null <<EOF

[Unit]
Description=strided
After=network-online.target

[Service]
User=$USER
ExecStart=$(which strided) start
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
sudo systemctl enable strided && \
sudo systemctl restart strided && sudo journalctl -u strided -f -o cat
```

#### state sync
```
sudo systemctl stop strided
strided tendermint unsafe-reset-all --home $HOME/.stride --keep-addr-book
SNAP_RPC="https://stride-testnet.nodejumper.io:443"
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
peers="17b24705533d633cb3501233a18912ae6cc36a41@stride-testnet.nodejumper.io:28656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.stride/config/config.toml
sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.stride/config/config.toml
sudo systemctl restart strided
sudo journalctl -u strided -f --no-hostname -o cat
```
#### Snapshot
```
height: 80,622 (5 hours ago); size: 5.2GB; pruning: custom/100/0/10; indexer: null

исходник: https://nodejumper.io/stride-testnet/sync

sudo systemctl stop strided
strided tendermint unsafe-reset-all --home $HOME/.stride --keep-addr-book
cd $HOME/.stride
rm -rf data
SNAP_NAME=$(curl -s https://snapshots2-testnet.nodejumper.io/stride-testnet/ | egrep -o ">STRIDE-TESTNET-2.*\.tar.lz4" | tr -d ">")
curl https://snapshots2-testnet.nodejumper.io/stride-testnet/${SNAP_NAME} | lz4 -dc - | tar -xf -
sudo systemctl restart strided
sudo journalctl -u strided -f --no-hostname -o cat
```
Ждем синхронизации, создадим или восстановим кошелек:

#### создать кошелек( сохраняем мнемонику в безопасное место), 
```
strided keys add <name_wallet> --keyring-backend os
# восстановить кошелек (после команды вставить seed)
strided keys add <name_wallet> --recover --keyring-backend os
# подключить кошелек ledger
strided keys add <name_wallet> --ledger 
токены просим в дискорде в канале #token-faucet, пример:

$faucet-stride:stride.....
# проверить баланс
strided q bank balances <address>
```
#### Проверим синхронизацию и проверяем блоки:
```
# проверить блоки
strided status 2>&1 | jq ."SyncInfo"."latest_block_height"
# проверить логи
sudo journalctl -u strided -f -o cat
# проверить статус
curl localhost:26657/status

Ждем значение "catching_up": false
```

#### Создаем валидатора:
```
strided tx staking create-validator \
--chain-id STRIDE-TESTNET-2 \
--commission-rate 0.05 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.1 \
--min-self-delegation "1000000" \
--amount 1000000ustrd \
--pubkey $(strided tendermint show-validator) \
--moniker "<name_moniker>" \
--from <name_wallet> \
--fees 555ustrd
```
#### Cохраняем priv_validator_key.json !!!
```
Делегация токенов (1 STRD):

strided tx staking delegate <valoper_address> 1000000ustrd --from <name_wallet> --fees 500ustrd -y
# выбраться из тюрьмы
strided tx slashing unjail --from <name_wallet> --fees 500ustrd -y
# отправить монеты на другой адрес
strided tx bank send <name_wallet> <address> 1000000ustrd --fees 500ustrd -y
# ределегирование на другого валидатора
strided tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000ustrd --from <name_wallet> --fees 500ustrd -y
# собрать комиссионные + реварды
strided tx distribution withdraw-rewards <valoper_address> --from <name_wallet> --fees 500ustrd --commission -y
```
#### Edit validator 
```
BINARY tx staking edit-validator \
  --chain-id "CHAIN_NAME" \
  --moniker "MONIKER" \
  --identity "IDENTITY" \
  --details "DETAILS" \
  --from "WALLET_NAME"
```
