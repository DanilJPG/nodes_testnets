## Установка полного узла
Website: https://celestia.org/

Docs: https://docs.celestia.org/

Explorers: https://celestia.explorers.guru/

Github: https://github.com/celestiaorg

#### Обновление и установка "ключей" и утилит
```
sudo apt update && sudo apt upgrade -y \
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu -y
```
#### Установка Go
```
cd $HOME
ver="1.19.1"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile

go version
```
#### Установка Celestia App
```
cd $HOME
rm -rf celestia-app
git clone https://github.com/celestiaorg/celestia-app.git
cd celestia-app
git checkout v0.6.0
make install
```
```
celestia-appd version
```
```
git clone https://github.com/celestiaorg/networks
```
#### Устновка ноды
```
cd $HOME
rm -rf networks
git clone https://github.com/celestiaorg/networks.git
```
#### задаем переменные
```
CELESTIA_NODENAME="<name_operator>"
CELESTIA_WALLET="<name_wallet>"

CELESTIA_CHAIN="mamaki
```
```
echo 'export CELESTIA_CHAIN='$CELESTIA_CHAIN >> $HOME/.bash_profile
echo 'export CELESTIA_NODENAME='${CELESTIA_NODENAME} >> $HOME/.bash_profile
echo 'export CELESTIA_WALLET='${CELESTIA_WALLET} >> $HOME/.bash_profile
source $HOME/.bash_profile
```
#### Инициализация
```
celestia-appd init $CELESTIA_NODENAME --chain-id $CELESTIA_CHAIN 
```
```
cp $HOME/networks/mamaki/genesis.json $HOME/.celestia-app/config/
```
#### Настройка клиента
```
celestia-appd config chain-id $CELESTIA_CHAIN
celestia-appd config keyring-backend test
```

#### Сервисный файл
```
sudo tee $HOME/celestia-appd.service > /dev/null <<EOF
[Unit]
  Description=celestia-appd Cosmos daemon
  After=network-online.target
[Service]
  User=$USER
  ExecStart=$(which celestia-appd) start
  Restart=on-failure
  RestartSec=3
  LimitNOFILE=65535
[Install]
  WantedBy=multi-user.target
EOF

sudo mv $HOME/celestia-appd.service /etc/systemd/system/
```
#### Запуск
```
sudo systemctl enable celestia-appd
sudo systemctl daemon-reload
sudo systemctl restart celestia-appd && journalctl -u celestia-appd -f -o cat
```
