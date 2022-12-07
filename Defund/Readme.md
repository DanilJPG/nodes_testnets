### На данный момент времени 28.10 в 03-00 по МСК можно подать Gentx для отбора в актив сет до 29.10
#### Обновляемся и устанавливаем зависимости
```Shell
sudo apt update && sudo apt upgrade -y && \
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```
#### Установка Go
```Shell
ver="1.19" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

#### Клонируем репозиторий 
```Shell
git clone https://github.com/defund-labs/defund
cd defund
git checkout v0.1.0-alpha
make install
```

#### Инициализируем 
```Shell
defundd init <moniker> --chain-id defund-private-2 \
defundd config chain-id defund-private-2
```

#### Скачиваем Genesis и Addrbook
```Shell
wget -O /root/.defund/config//genesis.json "https://github.com/defund-labs/testnet/blob/main/defund-private-2/pre-genesis.json"
wget -O $HOME/.defund/config/addrbook.json https://raw.githubusercontent.com/sowell-owen/addrbooks/main/defund/addrbook.json
```
#### Правим конфиг
```Shell
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" /root/.defund/config/config.toml

sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025ufetf\"/;" /root/.defund/config/app.toml

PEERS=``
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" ~/.lambdavm/config/config.toml

SEEDS=``
sed -i.bak -e "s/^seeds =.*/seeds = \"$SEEDS\"/" ~/.lambdavm/config/config.toml
```

#### Создаем сервисный файл
```Shell
sudo tee /etc/systemd/system/defund.service > /dev/null <<EOF
[Unit]
Description=defund
After=network-online.target

[Service]
User=$USER
ExecStart=$(which defund) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
#### Запуск
```Shell
systemctl daemon-reload && \
systemctl enable defund && \
systemctl restart defund && journalctl -u defund -f -o cat
```
#### Wallet
```Shell
# создать кошелек
defund keys add <name_wallet> --keyring-backend os

# восстановить кошелек (после команды вставить seed)
defund keys add <name_wallet> --recover --keyring-backend os
```

#### Validator
```Shell
defund tx staking create-validator \
--amount 1000000ufetf \
--pubkey $(defund tendermint show-validator) \
--moniker "<moniker>" \
--chain-id defund-private-2 \
--commission-rate "0.05" \
--min-self-delegation "1000000" \
--commission-max-rate "0.20" \
--commission-max-change-rate "0.01" \
--from garfield_wallet \
--gas=auto \
--identity "your keybase" \ 
--details "Your validator motto" \
--fees 5000ulamb
```
#### Gentx
```Shell
Add/recover keys
To create new keypair - make sure you save the mnemonics!

defundd keys add <key-name> 

 Instructions for Genesis Validators
Create Gentx
1. Add genesis account:

defundd add-genesis-account <key-name> 100000000ufetf

2. Create Gentx

defundd gentx <key-name> 90000000ufetf \
--chain-id defund-private-2 \
--moniker="<moniker>" \
--commission-max-change-rate=0.01 \
--commission-max-rate=0.20 \
--commission-rate=0.05 \
--details="XXXXXXXX" \
--security-contact="XXXXXXXX" \
--website="XXXXXXXX"

+ Submit PR with Gentx and peer id
+ Copy the contents of ${HOME}/.defund/config/gentx/gentx-XXXXXXXX.json.
+ Fork https://github.com/defund-labs/testnet
+ Create a file gentx-{{VALIDATOR_NAME}}.json under the defund-private-2/gentx/ folder in the forked repo, paste the copied text into the file.
+ Create a Pull Request to the main branch of the repository
+ Await further instruction!
```
#### Delete 
 ```Shell
sudo systemctl stop defund && \
sudo systemctl disable defund && \
rm /etc/systemd/system/defund.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf .defund defund && \
rm -rf $(which defund)
 ```