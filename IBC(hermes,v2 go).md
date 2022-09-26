# Hermes
### Setting up IBC between SEI and Uptick

#### Для начала нужно убедиться в том что ваши ноды синхронизированы для обоих проектов, RPC должны быть открыты.

#### Проверьте и запишите RPC конфигурацию в файле config.toml и GPRC конфигурацию в app.toml для обоих проектов.

Пример:
```
RPC:
laddr = "tcp://0.0.0.0:12657"
GPRC:
address = "0.0.0.0:12090"
```
Для каждой цепочки вам нужно будет иметь кошельки и мнемоники, на которых есть немного токенов каждой сети. Эти кошельки будут использоваться для выполнения всех функций ретранслятора

#### Обновляемся:
```
sudo apt update && sudo apt upgrade -y
Зависимости:

sudo apt install unzip -y
```
#### Настроим переменные окружения:

Все приведенные ниже настройки являются лишь примером для ретранслятора IBC между sei atlantic-1 uptickuptick_7776-1. 

#### Укажите имя 
```
RELAYER_NAME='name'
```
#### SEI:
В строка с RPC и GPRC введите свои значения. В `<mnemonic>`

нужно указать свою seed фразу от кошелька.
```
CHAIN_ID_A='atlantic-1'
RPC_ADDR_A='127.0.0.1:26657'
GRPC_ADDR_A='0.0.0.0:9090'
ACCOUNT_PREFIX_A='sei'
TRUSTING_PERIOD_A='2days'
DENOM_A='usei'
MNEMONIC_A='<mnemonic>'
```
#### Uptick:
Аналогично повторите операцию со значениями для настройки Uptick:
```
CHAIN_ID_B='uptick_7776-1'
RPC_ADDR_B='127.0.0.1:15657'
GRPC_ADDR_B='0.0.0.0:15090'
ACCOUNT_PREFIX_B='uptick'
TRUSTING_PERIOD_B='14days'
DENOM_B='auptick'
MNEMONIC_B='<mnemonic>'
```
#### Скачиваем Hermes
```
cd $HOME
wget https://github.com/informalsystems/ibc-rs/releases/download/v1.0.0-rc.0/hermes-v1.0.0-rc.0-x86_64-unknown-linux-gnu.zip
unzip hermes-v1.0.0-rc.0-x86_64-unknown-linux-gnu.zip
sudo mv hermes /usr/local/bin
hermes version
mkdir $HOME/.hermes
```
#### Создайте конфигурацию hermes
Сгенерируем конфигурационный файл hermes, используя переменные, которые мы определили выше. Так же понадобится web конфигурация для обоих сетей в файле app.toml
```
sudo tee $HOME/.hermes/config.toml > /dev/null <<EOF
[global]
log_level = 'info'

[mode]

[mode.clients]
enabled = true
refresh = true
misbehaviour = true

[mode.connections]
enabled = false

[mode.channels]
enabled = false

[mode.packets]
enabled = true
clear_interval = 100
clear_on_start = true
tx_confirmation = true

[rest]
enabled = true
host = '127.0.0.1'
port = 3000

[telemetry]
enabled = true
host = '127.0.0.1'
port = 3001

[[chains]]
### CHAIN_A ###
id = '${CHAIN_ID_A}'
rpc_addr = 'http://${RPC_ADDR_A}/'
grpc_addr = 'http://${GRPC_ADDR_A}/'
websocket_addr = 'ws://${RPC_ADDR_A}/websocket'
rpc_timeout = '10s'
account_prefix = '${ACCOUNT_PREFIX_A}'
key_name = 'wallet'
address_type = { derivation = 'cosmos' }
store_prefix = 'ibc'
default_gas = 100000
max_gas = 600000
gas_price = { price = 0.001, denom = '${DENOM_A}' }
gas_multiplier = 1.1
max_msg_num = 30
max_tx_size = 2097152
clock_drift = '5s'
max_block_time = '30s'
trusting_period = '${TRUSTING_PERIOD_A}'
trust_threshold = { numerator = '1', denominator = '3' }
memo_prefix = '${RELAYER_NAME} Relayer'

[[chains]]
### CHAIN_B ###
id = '${CHAIN_ID_B}'
rpc_addr = 'http://${RPC_ADDR_B}/'
grpc_addr = 'http://${GRPC_ADDR_B}/'
websocket_addr = 'ws://${RPC_ADDR_B}/websocket'
rpc_timeout = '10s'
account_prefix = '${ACCOUNT_PREFIX_B}'
key_name = 'wallet'
address_type = { derivation = 'cosmos' }
store_prefix = 'ibc'
default_gas = 100000
max_gas = 600000
gas_price = { price = 0.001, denom = '${DENOM_B}' }
gas_multiplier = 1.1
max_msg_num = 30
max_tx_size = 2097152
clock_drift = '5s'
max_block_time = '30s'
trusting_period = '${TRUSTING_PERIOD_B}'
trust_threshold = { numerator = '1', denominator = '3' }
memo_prefix = '${RELAYER_NAME} Relayer'
EOF
```
Прежде чем продолжить, пожалуйста, проверьте правильность вашей конфигурации
```
hermes health-check
```
Работоспособный результат должен выглядеть следующим образом:
```
2022-07-21T19:38:15.571398Z  INFO ThreadId(01) using default configuration from '/root/.hermes/config.toml'
2022-07-21T19:38:15.573884Z  INFO ThreadId(01) [atlantic-1] performing health check...
2022-07-21T19:38:15.614273Z  INFO ThreadId(01) chain is healthy chain=atlantic-1
2022-07-21T19:38:15.614313Z  INFO ThreadId(01) [uptick_7776-1] performing health check...
2022-07-21T19:38:15.627747Z  INFO ThreadId(01) chain is healthy chain=uptick_7776-1
Success: performed health check for all chains in the config
```
#### Восстановление кошельков с помощью мнемонических файлов
Прежде чем приступить к этому шагу, пожалуйста, убедитесь, что вы создали и закинули токены на кошельки в каждой цепочке
```
sudo tee $HOME/.hermes/${CHAIN_ID_A}.mnemonic > /dev/null <<EOF
${MNEMONIC_A}
EOF
sudo tee $HOME/.hermes/${CHAIN_ID_B}.mnemonic > /dev/null <<EOF
${MNEMONIC_B}
EOF
hermes keys add --chain ${CHAIN_ID_A} --mnemonic-file $HOME/.hermes/${CHAIN_ID_A}.mnemonic
hermes keys add --chain ${CHAIN_ID_B} --mnemonic-file $HOME/.hermes/${CHAIN_ID_B}.mnemonic
```
Успешный вывод должен выглядеть следующим образом:
```
2022-07-21T19:54:13.778550Z  INFO ThreadId(01) using default configuration from '/root/.hermes/config.toml'
Success: Restored key 'wallet' (sei1eardm7w7v9el9d3khkwlj9stdj9hexnfv8pea8) on chain atlantic-1
2022-07-21T19:54:14.956171Z  INFO ThreadId(01) using default configuration from '/root/.hermes/config.toml'
Success: Restored key 'wallet' (uptick1ypnerpxuqezq2vfqxm74ddkuqnektveezh5uaa) on chain uptick_7776-1
```

#### Сервисный файл для hermes
```
sudo tee /etc/systemd/system/hermesd.service > /dev/null <<EOF
[Unit]
Description=hermes
After=network-online.target

[Service]
User=$USER
ExecStart=$(which hermes) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
```
sudo systemctl daemon-reload
sudo systemctl enable hermesd
sudo systemctl restart hermesd
```

#### Проверьте журналы hermes
```
journalctl -u hermesd -f -o cat
```
### Найти бесплатные каналы
```
hermes query channels --chain ${CHAIN_ID_A}
hermes query channels --chain ${CHAIN_ID_B}
```
#### Зарегистрировать канал
```
hermes create channel --a-chain ${CHAIN_ID_A} --b-chain ${CHAIN_ID_B} --a-port transfer --b-port transfer --order unordered --new-client-connection
```
Выходные данные должны предоставить вам всю информацию о каналах, которые были созданы для текущих цепочек.

#### Выполните передачу токена с помощью IBC Relayer
Все приведенные ниже команды являются лишь примерами
```
seid tx ibc-transfer transfer transfer channel-162 uptick1jqpaf0vgzlxvjx5meq8huweuv2nguqe2ugvd9v 666usei --from $WALLET --fees 200usei
```
Отправлять токены auptick с uptick_7776-1на atlantic-1
```
uptickd tx ibc-transfer transfer transfer channel-5 sei1828et4w3u52na4h8m3mp8r22vjylxeaa2zq5ed 666auptick --from $WALLET --fees 200auptick
```


# g0 V2(между Stride и GAIA)

Перед настройкой ретранслятора вам необходимо убедиться, что у вас уже есть:

1.Полностью синхронизированные узлы RPC для каждого проекта Cosmos, к которому вы хотите подключиться

2.Ваши кошельки должны быть пополнены через кран

3. Точки RPC должны быть открыты и доступны из экземпляра ретранслятора (indexer=kv)

#### 8.1 Настройка 

#### STRIDE
sed -i -e "s/^indexer *=.*/indexer = \"kv\"/" $HOME/.stride/config/config.toml
# GAIA
sed -i -e "s/^indexer *=.*/indexer = \"kv\"/" $HOME/.gaia/config/config.toml  
RPC конфигурация нод проектов, находящихся вconfig.toml file:

#### STRIDE
laddr = "tcp://0.0.0.0:26657" in $HOME/.stride/config/config.toml
#### GAIA
laddr = "tcp://0.0.0.0:23657" in $HOME/.gaia/config/config.toml  
RELAYER_ID='kjnodes|#8455'            # используйте свой ник из Discorda
STRIDE_RPC_ADDR='127.0.0.1:26657'    # ресерчьте config.toml по слову laddr
GAIA_RPC_ADDR='127.0.0.1:23657       # ресерчьте config.toml по слову laddr
#### обновляемся
sudo apt update && sudo apt upgrade -y
#### установка зависимостей
sudo apt install curl build-essential git wget jq make gcc tmux chrony -y
#### Установка Go
```
if ! [ -x "$(command -v go)" ]; then
  ver="1.18.3"
  cd $HOME
  wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
  sudo rm -rf /usr/local/go
  sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
  rm "go$ver.linux-amd64.tar.gz"
  echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
  source ~/.bash_profile
fi
```
8.2 Загрузка и сборка бинарника и инициализация relayer go v2
```
git clone https://github.com/cosmos/relayer.git
cd relayer && git checkout v2.0.0-rc4
make install
rly config init --memo $RELAYER_ID
sudo mkdir $HOME/.relayer/chains
sudo mkdir $HOME/.relayer/paths
```
#### 8.3 Создаем сервисные файлы для каждой цепочки

#### Создаем конфиг файл для STRIDE
```
sudo tee $HOME/.relayer/chains/stride.json > /dev/null <<EOF
{
  "type": "cosmos",
  "value": {
    "key": "wallet",
    "chain-id": "STRIDE-TESTNET-4",
    "rpc-addr": "http://${STRIDE_RPC_ADDR}",
    "account-prefix": "stride",
    "keyring-backend": "test",
    "gas-adjustment": 1.2,
    "gas-prices": "0.000ustrd",
    "gas": 200000,
    "timeout": "20s",
    "trusting-period": "8h",
    "output-format": "json",
    "sign-mode": "direct"
  }
}
EOF
```
#### Создаем конфиг файл для GAIA
```
sudo tee $HOME/.relayer/chains/gaia.json > /dev/null <<EOF
{
  "type": "cosmos",
  "value": {
    "key": "wallet",
    "chain-id": "GAIA",
    "rpc-addr": "http://${GAIA_RPC_ADDR}",
    "account-prefix": "cosmos",
    "keyring-backend": "test",
    "gas-adjustment": 1.2,
    "gas-prices": "0.000uatom",
    "gas": 200000,
    "timeout": "20s",
    "trusting-period": "8h",
    "output-format": "json",
    "sign-mode": "direct"
  }
}
EOF
```
#### Загрузка каждого конфиг файла в рестранслятор
```
rly chains add --file=$HOME/.relayer/chains/stride.json stride
rly chains add --file=$HOME/.relayer/chains/gaia.json gaia
```
#### check
```
rly chains list
```
Ожидаем:
![image](https://user-images.githubusercontent.com/57448493/192292679-7fdc6ec2-75dc-4e52-bb3e-685f3cfc1454.png)


#### Загружаем кошельки в рестранслятор 
```
rly keys restore stride wallet "вставляем мнемонику кошелька STRIDE"
rly keys restore gaia wallet "вставляем мнемонику кошелька GAIA"
```
#### Проверка, должны на выходе появится ваши суммы на кошельках
```
rly q balance stride
rly q balance gaia
Создаем json файл с путем STRIDE-TESTNET-4 и GAIA
sudo tee $HOME/.relayer/paths/stride-gaia.json > /dev/null <<EOF
{
  "src": {
    "chain-id": "STRIDE-TESTNET-4",
    "client-id": "07-tendermint-0",
    "connection-id": "connection-0"
  },
  "dst": {
    "chain-id": "GAIA",
    "client-id": "07-tendermint-0",
    "connection-id": "connection-0"
  },
  "src-channel-filter": {
    "rule": "allowlist",
    "channel-list": ["channel-0", "channel-1", "channel-2", "channel-3", "channel-4"]
  }
}
EOF
```
#### Добавляем этот путь в relayer
```
rly paths add STRIDE-TESTNET-4 GAIA stride-gaia --file $HOME/.relayer/paths/stride-gaia.json
```
#### Проверка пути
```
rly paths list
```
Ожидаем:
![image](https://user-images.githubusercontent.com/57448493/192292725-188ebd7f-0fe5-4a9b-b471-b450b8ea2241.png)


#### Создаем сервисный файл
```
sudo tee /etc/systemd/system/relayerd.service > /dev/null <<EOF
[Unit]
Description=GO Relayer v2 Service
After=network.target
[Service]
Type=simple
User=$USER
ExecStart=$(which rly) start stride-gaia -p events
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```
#### 8.5 Запуск

#### Если до этого вы использовали relyer через hermes, то останавливаем его 
```
sudo systemctl stop hermesd
sudo systemctl disable hermesd
```
#### Запускаем relayer go v2
```
sudo systemctl daemon-reload
sudo systemctl enable relayerd
sudo systemctl start relayerd
```
#### наблюдаем за логами
```
journalctl -u relayerd -f -o cat
```
![image](https://user-images.githubusercontent.com/57448493/192293173-f1e0005c-32ec-46b4-8168-aef17c01c319.png)

Переходим в любой эксплорер, находим свой кошелек и ищем информацию о клиенте relayer go v2.

Форкаем репозиторий 
![image](https://user-images.githubusercontent.com/57448493/192292802-adab095e-9b8a-49d3-948b-58a0671a5e5f.png)


Переходим в форканный репозиторий и добавляем файл (либо создаем новый) с путями, для каждого пути свой файл соответсвенно
```
relayer/configs/stride/chains/gaia.json
relayer/configs/stride/chains/stride.json
relayer/configs/stride/paths/stride-gaia.json
```
![image](https://user-images.githubusercontent.com/57448493/192292876-b443719e-d5d5-4627-986f-7bbbb98460b5.png)
![image](https://user-images.githubusercontent.com/57448493/192292937-261c292d-eb7e-4f91-bb7c-5e9ac85675e4.png)
![image](https://user-images.githubusercontent.com/57448493/192292958-46764ebe-ba3a-4ef9-84d2-350591425c0b.png)


