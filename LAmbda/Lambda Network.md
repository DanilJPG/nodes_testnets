## Lambda Mainnet

Dicord: https://discord.gg/lambdanetwork

Docs: https://docs.lambda.im/validators/testnet.html

#### Обновляемся и устанавливаем зависимости
```
sudo apt update && sudo apt upgrade -y && \
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```
#### Установка Go
```
ver="1.19.1"
cd $HOME
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
```

#### Клонирование репозитория 
```
git clone https://github.com/LambdaIM/lambdavm.git
cd lambdavm
make install
```

#### Инициализация 
```
lambdavm init <your_custom_moniker> --chain-id lambda_92000-1 \
lambdavm config chain-id lambda_92000-1
```

#### Скачиваем genesisi
```
wget -O /root/.lambdavm/config//genesis.json "https://raw.githubusercontent.com/LambdaIM/mainnet/main/lambda_92000-1/genesis.json"
#check

`sha256sum genesis.json`
# 1ff02001539bc1e9828fe170006f055c04df280c61c4ca9ecc9e7b6a272b7777  genesis.json
```

#### Правим конфиг файл
```
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" /root/.lambdavm/config/config.toml

sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025ulamb\"/;" /root/.lambdavm/config/app.toml

PEERS=`curl -sL https://raw.githubusercontent.com/LambdaIM/mainnet/main/lambda_92000-1/peers.txt | sort -R | head -n 10 | awk '{print $1}' | paste -s -d, -`

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" ~/.lambdavm/config/config.toml

SEEDS=`curl -sL https://raw.githubusercontent.com/LambdaIM/mainnet/main/lambda_92000-1/seeds.txt | awk '{print $1}' | paste -s -d, -`
sed -i.bak -e "s/^seeds =.*/seeds = \"$SEEDS\"/" ~/.lambdavm/config/config.toml
```

#### Создаем сервисный файл
```
sudo tee /etc/systemd/system/lambdavm.service > /dev/null <<EOF
[Unit]
Description=lambdavm
After=network-online.target
[Service]
User=$USER
ExecStart=$(which lambdavm) start
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
systemctl enable lambdavm && \
systemctl restart lambdavm && journalctl -u lambdavm -f -o cat
```

#### Создание валидатора 
```
lambdavm tx staking create-validator \
--amount 1000000000000000000ulamb \
--pubkey $(lambdavm tendermint show-validator) \
--moniker "garfield" \
--chain-id lambda_92000-1 \
--commission-rate "0.05" \
--min-self-delegation "1000000" \
--commission-max-rate "0.20" \
--commission-max-change-rate "0.01" \
--from garfield_wallet \
--gas=auto \
--fees 5000ulamb
```
#### Wallet 
```
# создать кошелек
empowerd keys add $WALLET --keyring-backend os

# восстановить кошелек (после команды вставить seed)
empowerd keys add $WALLET --recover --keyring-backend os

# экспорт в метамаск(просмотр закрытого ключа)
lambdavm keys unsafe-export-eth-key garfield_wallet

#экспортировать кошелек из метамаска
lambdavm keys unsafe-import-eth-key [account name] [private key]
```
#### Полезные команды
```
# проверить блоки
lambdavm status 2>&1 | jq ."SyncInfo"."latest_block_height"

# проверить логи
journalctl -u lambdavm -f -o cat

# проверить статус
curl localhost:26657/status

# проверить баланс
lambdavm q bank balances <address>

# проверить pubkey валидатора
empowerd tendermint show-validator
```

#### Транзакции
```
# собрать реварды со всех валидаторов, которым делегировали (без комиссии)
lambdavm tx distribution withdraw-all-rewards --from <name_wallet> --fees 5000ulamb -y

# собрать реварды c отдельного валидатора или реварды + комиссию со своего валидатора
lambdavm tx distribution withdraw-rewards <valoper_address> --from <name_wallet> --fees 5000ulamb --commission -y

# заделегировать себе в стейк еще (так отправляется 1 монетa)
lambdavm tx staking delegate <valoper_address> 1000000ulamb --from <name_wallet> --fees 5000ulamb -y

# ределегирование на другого валидатора
lambdavm tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000ulamb --from <name_wallet> --fees 5000ulamb -y

# unbond 
lambdavm tx staking unbond <addr_valoper> 1000000ulamb --from <name_wallet> --fees 5000ulamb -y

# отправить монеты на другой адрес
lambdavm tx bank send <name_wallet> <address> 1000000ulamb --fees 5000umpwr -y
```

#### Удаление
```
systemctl stop lambdavm && \
systemctl disable lambdavm && \
rm /etc/systemd/system/lambdavm.service && \
systemctl daemon-reload && \
cd $HOME && \
rm -rf .lambdavm lambdavm && \
rm -rf $(which lambdavm)
```
