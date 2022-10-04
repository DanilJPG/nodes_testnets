![image](https://user-images.githubusercontent.com/57448493/192142819-547c79d9-1a04-483b-866b-de47f121d9f5.png)

#### WebSite : https://islamiccoin.net/documentation

#### Discord: https://discord.gg/HCYUVZWp

#### Telegram: https://t.me/islamic_coin

#### Haqq позволяет запускать приложения с использованием функций ETH, но взаимодействия валидаторов в PoS происходят на основе функций Tendermint.

8 сентября выходит обновление TestEdge,на данный момент можно подать gentx для возможного получения делегации в майннет. Выберут 100 валидаторов. Все что вы имеете сейчас на балансах никак не влияет на дальнейшее место в активе. Команда писала, что увеличит количество активных мест, но до этого надо еще дожить.

Проект планирует предоставлять инновации и занимается благотворительностью, создание приложений на Tendermint облегчит создание и мониторинг систем. Свойства ETH помогут масштабироваться и увеличить скорость отклика и транзакций клиентов в будущем.

#### Обновление 04.10.2022 на высоте 355555
```
cd && rm -rf haqq
git clone https://github.com/haqq-network/haqq
cd haqq && git checkout v1.2.0
make build
mv ./build/haqqd $(which haqqd)
haqqd version # must be v1.2.0
systemctl restart haqqd
journalctl -u haqqd -f -o cat
```

### 1. Обновляемся и скачиваем зависимости:
```
apt update && apt upgrade -y
sudo apt-get install curl git make gcc liblz4-tool build-essential jq -y
```

### 2. Установим GO:
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

### 3. Загрузка и формирование бинарника: 
```
cd $HOME
git clone -b v1.0.3 https://github.com/haqq-network/haqq
cd haqq
make install
haqqd version:
"1.0.3" 58215364d5be4c9ab2b17b2a80cf89f10f6de38a
```

### 4. Инициализируем ноду
```
haqqd init <name_moniker> --chain-id haqq_53211-1
```
Приведенная выше команда создает все конфигурационные файлы, необходимые для работы вашего узла, а также файл genesis по умолчанию, который определяет начальное состояние сети. По умолчанию все эти конфигурационные файлы находятся в ~/.haqq, но вы можете изменить расположение этой папки, передав флаг --home.

### 5. Заполнение конфигурационных файлов

#### 5.1 Скачиваем genesis
```
wget -O $HOME/.haqqd/config/genesis.json "https://raw.githubusercontent.com/haqq-network/validators-contest/master/genesis.json"
```
#### 5.2 Скачиваем addrbook
```
wget -O $HOME/.haqqd/config/addrbook.json "https://github.com/StakeTake/guidecosmos/blob/main/haqq/haqq_53211-1/README.md#add-addrbook"
```
#### 5.3 Редактируем config.toml

# правим конфиг, благодаря чему мы можем больше не использовать флаг chain-id для каждой команды CLI в client.toml
```
haqqd config chain-id haqq_54211-2
```
# настраиваем минимальную цену за газ в app.toml
```
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0aISLM\"/;" ~/.haqqd/config/app.toml
```
# добавляем seeds/bpeers/peers в config.toml
```
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.haqqd/config/config.toml
```
```
peers="b3ce1618585a9012c42e9a78bf4a5c1b4bad1123@65.21.170.3:33656,952b9d918037bc8f6d52756c111d0a30a456b3fe@213.239.217.52:29656,85301989752fe0ca934854aecc6379c1ccddf937@65.109.49.111:26556,d648d598c34e0e58ec759aa399fe4534021e8401@109.205.180.81:29956,f2c77f2169b753f93078de2b6b86bfa1ec4a6282@141.95.124.150:20116,eaa6d38517bbc32bdc487e894b6be9477fb9298f@78.107.234.44:45656,37513faac5f48bd043a1be122096c1ea1c973854@65.108.52.192:36656,d2764c55607aa9e8d4cee6e763d3d14e73b83168@66.94.119.47:26656,fc4311f0109d5aed5fcb8656fb6eab29c15d1cf6@65.109.53.53:26656,297bf784ea674e05d36af48e3a951de966f9aa40@65.109.34.133:36656,bc8c24e9d231faf55d4c6c8992a8b187cdd5c214@65.109.17.86:32656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.haqqd/config/config.toml
```
```
seeds="62bf004201a90ce00df6f69390378c3d90f6dd7e@seed2.testedge2.haqq.network:26656,23a1176c9911eac442d6d1bf15f92eeabb3981d5@seed1.testedge2.haqq.network:26656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.haqqd/config/config.toml
```
#### 6. Создание сервисного файла
```
sudo tee /etc/systemd/system/haqqd.service > /dev/null <<EOF
[Unit]
Description=haqqd
After=network-online.target

[Service]
User=$USER
ExecStart=$(which haqqd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

```                                                          
#### 7. Запуск
```
sudo systemctl daemon-reload && \
sudo systemctl enable haqqd && \
sudo systemctl restart haqqd && sudo journalctl -u haqqd -f -o cat
```
                                                             
                                                             
#### 8. Кошелек
```
# создание
haqqd keys add <name_wallet> --keyring-backend os

# восстановление
haqqd keys add <name_wallet> --recover --keyring-backend os
```

#### 9. Валидатор 
```
haqqd tx staking create-validator \
--chain-id haqq_53211-1 \
--commission-rate 0.05 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.1 \
--min-self-delegation "1000000" \
--amount 1000000000000000000aISLM \
--pubkey $(haqqd tendermint show-validator) \
--moniker "<name_moniker>" \
--from <name_wallet> \
--fees 555aISLM
Обязательно сохраните priv_validator_key.json
```
Полезные команды по валидатору 

#### Sync
```
# проверить блоки
haqqd status 2>&1 | jq ."SyncInfo"."latest_block_height"
# проверить логи
journalctl -u haqqd -f -o cat
journalctl --lines=100 --follow --unit haqqd
# проверить статус
curl localhost:26657/status
# проверить баланс
haqqd q bank balances <address>
```

  
### Delegetion
```
# заделегировать себе в стейк еще (так отправляется 1 монетa)
haqqd tx staking delegate <valoper_address> 1000000000000000000aISLM --from <name_wallet> --fees 500aISLM -y
# ределегирование на другого валидатора
haqqd tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000aISLM --from <name_wallet> --fees 500aISLM -y
# unbond 
haqqd tx staking unbond <addr_valoper> 1000000aISLM --from <name_wallet> --fees 500aISLM -y
# отправить монеты на другой адрес
haqqd tx bank send <name_wallet> <address> 1000000aISLM --fees 500aISLM -y
# выбраться из тюрьмы
haqqd tx slashing unjail --from <name_wallet> --fees 500aISLM -y
```

  
### Governance
```
# список proposals
haqqd q gov proposals
# посмотреть результат голосования
haqqd q gov proposals --voter <ADDRESS>
# проголосовать за предложение 
haqqd tx gov vote 1 yes/no --from <name_wallet> --fees 555aISLM
Удалить ноду
```

```
sudo systemctl stop haqqd && \
sudo systemctl disable haqqd && \
rm /etc/systemd/system/haqqd.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf .haqqd haqq && \
rm -rf $(which haqqd)
```
