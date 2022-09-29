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
ollod keys list
#### Скачиваем генезис
```
wget $HOME/.ollod/config/genesis.json https://raw.githubusercontent.com/OllO-Station/ollo/master/networks/ollo-testnet-0/genesis.json
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

peers="0b4474bc96d72586e1be1860db731522d05fdeef@181.41.142.78:11523"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.ollo/config/config.toml

seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.ollo/config/config.toml
```


```
sudo tee /etc/systemd/system/ollod.service > /dev/null <<EOF
[Unit]
Description=ollod
After=network-online.target
```
```
[Service]
User=$USER
ExecStart=$(which ollod) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535
```
[Install]
WantedBy=multi-user.target
EOF
                     
```
sudo systemctl daemon-reload && \
sudo systemctl enable ollod && \
sudo systemctl restart ollod && sudo journalctl -u ollod -f -o cat
```
#### Проверим логи
```
journalctl -fu ollod -o cat
```
