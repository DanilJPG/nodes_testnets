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
