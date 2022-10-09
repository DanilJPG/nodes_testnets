#### Обновляем репозитории
```
sudo apt update && sudo apt upgrade -y
```
#### Устанавливаем необходимые утилиты
```
sudo apt install curl build-essential git wget jq make gcc tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```

#### Устанавливаем Go 
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

#### Устанавливаем бинарники
```
git clone https://github.com/sei-protocol/sei-chain.git && cd sei-chain
git checkout 1.1.1beta
make install

seid version --long | head
# version: 1.1.1beta
# commit: 9764e4d7b0fdbfacfca446c1a12a75df1693cd02
```
#### Инициализируем ноду, чтобы создать необходимые файлы конфигурации
```
seid init <name_moniker> --chain-id atlantic-1
```
#### Скачиваем Genesis
```
wget -O $HOME/.sei/config/genesis.json "https://raw.githubusercontent.com/sei-protocol/testnet/master/sei-incentivized-testnet/genesis.json"

# Проверим генезис
sha256sum ~/.sei/config/genesis.json
``` 
#### Проверяем, что состояние валидатора на начальном этапе
```
cd && cat .sei/data/priv_validator_state.json
{
  "height": "0",
  "round": 0,
  "step": 0
}

# если нет, то выполняем команду
seid tendermint unsafe-reset-all --home $HOME/.sei
```
#### Скачиваем addrbook
```
wget -O $HOME/.sei/config/addrbook.json "https://raw.githubusercontent.com/sei-protocol/testnet/master/sei-incentivized-testnet/addrbook.json"
```
#### Настраиваем конфигурацию ноды
```
# правим конфиг, благодаря чему мы можем больше не использовать флаг chain-id для каждой команды CLI в client.toml
seid config chain-id atlantic-1

# при необходимости настраиваем keyring-backend в client.toml
#seid config keyring-backend os

# настраиваем минимальную цену за газ в app.toml
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025usei\"/;" ~/.sei/config/app.toml

# добавляем seeds/bpeers/peers в config.toml
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.sei/config/config.toml

peers="e3b5da4caea7370cd85d7738eedaec8f56c5be28@144.76.224.246:36656,a37d65086e78865929ccb7388146fb93664223f7@18.144.13.149:26656,8ff4bd654d7b892f33af5a30ada7d8239d6f467b@91.223.3.190:51656,c4e8c9b1005fe6459a922f232dd9988f93c71222@65.108.227.133:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.sei/config/config.toml

#bpeers=""
#sed -i.bak -e "s/^bootstrap-peers *=.*/bootstrap-peers = \"$bpeers\"/" $HOME/.sei/config/config.toml

seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.sei/config/config.toml
```
#### Prunning
```
pruning="custom" && \
pruning_keep_recent="100" && \
pruning_keep_every="0" && \
pruning_interval="50" && \
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.sei/config/app.toml && \
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.sei/config/app.toml && \
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.sei/config/app.toml && \
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.sei/config/app.toml
```

#### Создаем сервисный файл
```
sudo tee /etc/systemd/system/seid.service > /dev/null <<EOF
[Unit]
Description=seid
After=network-online.target

[Service]
User=$USER
ExecStart=$(which seid) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
#### Start
```
sudo systemctl daemon-reload && \
sudo systemctl enable seid && \
sudo systemctl restart seid && sudo journalctl -u seid -f -o cat
```

#### создать кошелек
```
seid keys add <name_wallet>

# восстановить кошелек (после команды вставить seed)
seid keys add <name_wallet> --recover
Не забываем сохранить seed !!!
```

#### Создаем валидатора
```
seid tx staking create-validator \
--chain-id atlantic-1 \
--commission-rate 0.05 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.1 \
--min-self-delegation 1 \
--amount 1000000usei \
--pubkey $(seid tendermint show-validator) \
--moniker "<name_moniker>" \
--from <name_wallet> \
--fees 5550usei
```
```
Не забываем сохранить priv_validator_key.json !!!
```

#### Полезные команды
```
# проверить блоки
seid status 2>&1 | jq ."SyncInfo"."latest_block_height"

# проверить логи
sudo journalctl -u seid -f -o cat

# проверить статус
curl localhost:26657/status

# проверить баланс
seid q bank balances <address>

# проверить валидатора
seid query staking validator <valoper_address>
seid query staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker=="<name_moniker>")' | jq

# проверка информации по TX_HASH
seid query tx <TX_HASH>
```
#### Транзакции
```
# собрать комиссионные + реварды
seid tx distribution withdraw-rewards <valoper_address> --from <name_wallet> --fees 5555usei --commission -y

# заделегировать себе в стейк еще (так отправляется 1 монетa)
seid tx staking delegate <valoper_address> 1000000usei --from <name_wallet> --fees 5555usei -y

# ределегирование на другого валидатора
seid tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000usei --from <name_wallet> --fees 5555usei -y

# unbond 
seid tx staking unbond <addr_valoper> 1000000usei --from <name_wallet> --fees 5555usei -y

# отправить монеты на другой адрес
seid tx bank send <name_wallet> <address> 1000000usei --fees 5555usei -y

# выбраться из тюрьмы
seid tx slashing unjail --from <name_wallet> --fees 5555usei -y
```
#### Работа с кошельками
```
# вывести список кошельков
seid keys list

# показать ключ аккаунта
seid keys show <name_wallet> --bech acc

# показать ключ валидатора
seid keys show <name_wallet> --bech val

# показать ключ консенсуса
seid keys show <name_wallet> --bech cons

# запрос учетной записи
seid q auth account $(quicksilverd keys show <name_wallet> -a) -o text
```
#### удалить кошелек
```
seid keys delete <name_wallet>
```
#### Обновление
```
cd seid
git pull
git checkout ...
make install

# после перезагружаем ноду
```
#### Удалить ноду
```
sudo systemctl stop seid && \
sudo systemctl disable seid && \
rm /etc/systemd/system/seid.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf .sei .sei-chain sei-chain && \
rm -rf $(which seid)
```
#### Голосование
```
# список proposals
seid q gov proposals

# посмотреть результат голосования
seid q gov proposals --voter <ADDRESS>

# проголосовать за предложение 
seid tx gov vote 1 yes --from <name_wallet> --fees 5550usei

# внести депозит в предложение
seid tx gov deposit 1 1000000usei --from <name_wallet> --fees 5550usei
```
