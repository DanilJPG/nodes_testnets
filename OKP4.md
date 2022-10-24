#### Обновляемся и скачиваем зависимости

#### Клонируем репозиторий
```
git clone https://github.com/okp4/okp4d.git
cd okp4d
make install
```
#### Check version
```
okp4d version
2.2.0
```
#### Init
```
okp4d init garfield --chain-id okp4-testnet-1 \
okp4d config chain-id okp4-testnet-1
```

```
This will generate, in the $HOME/.okp4d folder, the following files:

config/app.toml: Application-related configuration file.
config/client.toml: Client-oriented configuration file (not used when running a node).
config/config.toml: Tendermint-related configuration file.
config/genesis.json: The network's genesis file.
config/node_key.json: Private key to use for node authentication in the p2p protocol.
config/priv_validator_key: Private key to use as a validator in the consensus protocol.
data: The node's database.
```
#### Скачиваем генезис
```
wget $HOME/.okp4d/config/genesis.json "https://raw.githubusercontent.com/okp4/networks/main/chains/nemeton/genesis.json"
```
#### Заполняем конфиг файл
```
wget $HOME/.okp4d/config/genesis.json "https://raw.githubusercontent.com/okp4/networks/main/chains/nemeton/genesis.json"
```

```
sudo tee /etc/systemd/system/okp4d.service > /dev/null <<EOF
[Unit]
Description=okp4d
After=network-online.target

[Service]
User=$USER
ExecStart=$(which okp4d) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
