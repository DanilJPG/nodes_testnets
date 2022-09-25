### Sirius Testnet

DWS предлагает полный набор инфраструктурных и прикладных услуг, которые позволяют запускать в децентрализованном облаке практически всё: от корпоративных приложений и проектов больших данных до социальных игр и мобильных приложений.

https://teletype.in/@garfield1/editor/v8nNLrPqkTR Официальный сайт, прошу ознакомится.

dewebd - это блокчейн-приложение, созданное с использованием Cosmos SDK v.0.45.0 и Tendermint v.0.34.15.

Вы можете запустить программу-валидатор, используя бинарный файл или скомпилировав его самостоятельно.

Рекомендуют модификации сервера: 4 ядра, 8 оперативки, 200 ссд;

Step 0A - Запуск полного узла / валидатора с использованием двоичных файлов
Загрузите последнюю версию (v0.2) бинарника с Github:

```
#Обновляем репозитории
sudo apt update && sudo apt upgrade -y
#Устанавливаем необходимые утилиты
sudo apt install curl build-essential git wget jq make gcc tmux nvme-cli -y
```

Для дальнейшей взаимодействии с программой потребуется установить Golang(одной командой)

```
wget https://golang.org/dl/go1.18.1.linux-amd64.tar.gz; \
rm -rv /usr/local/go; \
tar -C /usr/local -xzf go1.18.1.linux-amd64.tar.gz && \
rm -v go1.18.1.linux-amd64.tar.gz && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile && \
source ~/.bash_profile && \
go version
```

### Установка ноды
```
git clone https://github.com/deweb-services/deweb.git && cd deweb
git checkout v0.3.1
make install
проверьте версию: dewebd version 
0.3.1
```

#### Инициализируем ноду
Вместо <name_node> вставляем имя ноды, которое хотим присвоить
```
dewebd init <name_node> --chain-id deweb-testnet-sirius
```
#### Скачиваем Genesis
```
wget -O $HOME/.deweb/config/genesis.json "https://raw.githubusercontent.com/deweb-services/deweb/main/genesis.json"
```

```
Проверка версии:
sha256sum ~/.deweb/config/genesis.json 
5316dc5abf1bc46813b673e920cb6faac06850c4996da28d343120ee0d713ab9  /root/.deweb/config/genesis.json
```

#### Настраиваем ноду(разными командами)
```
cd $HOME
#настраиваем пиры
peers="F4C24583640A7BE659F8A6FA734F9ED61FB10CD9@78.137.4.44:42656" sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/; s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.deweb/config/config.toml

#настраиваем сид
sed -E -i 's/seeds = \".*\"/seeds = \"74d8f92c37ffe4c6393b3718ca531da8f0bf0594@seed1.deweb.services:26656\"/' $HOME/.deweb/config/config.toml

#сбор за газ
sed -E -i 's/minimum-gas-prices = \".*\"/minimum-gas-prices = \"0.001udws\"/' $HOME/.deweb/config/app.toml

###открываем порт
sudo ufw allow 26656
```

#### Создаем сервисный файл
```
sudo tee /etc/systemd/system/dewebd.service > /dev/null <<EOF
echo "[Unit]
Description=DWS Node
After=network-online.target
[Service]
User=${USER}
ExecStart=$(which dewebd) start
Restart=always
RestartSec=3
LimitNOFILE=4096
[Install] 
WantedBy=multi-user.target 
EOF
```
#### Запуск
```
sudo systemctl daemon-reload && \
sudo systemctl enable dewebd && \
sudo systemctl restart dewebd && sudo journalctl -u dewebd -f -o cat
```
Если имеются ошибки связанными с подсоединением пиров, стоит обратится за помощью в дискорд, и в закрепах искать нужную информацию

https://discord.gg/Kyuc3cvx

#### Проверяем синхронизацию и блоки
```
dewebd status 2>&1 | jq ."SyncInfo"."latest_block_height"
curl localhost:26657/status
```
Пока идет синхронизация можем создать и пополнить кошелек:
```
#создать кошелек
dewebd keys add <name_wallet>

#восстановить кошелек (после команды вставить seed)
dewebd keys add <name_wallet> --recover

#проверить баланс
dewebd q bank balances <deweb1...>
Если не может подключиться к пирам,то проверяйте: в дискорде проекта имеется канал peers
```
Если меняете,что то нужно изменять, то обращайтесь к остановке ноды:
```
#стопаем ноду, удаляем адресную книгу и сбрасываем данные
sudo systemctl stop dewebd
rm $HOME/.deweb/config/addrbook.json
dewebd unsafe-reset-all
#перезагружаем ноду
sudo systemctl restart dewebd && journalctl -u dewebd -f -o cat
После синхронизации, идем в дискорд просим токены(канал #faucet), 1 dws = 1000000 udws
```
#### Ставим валидатора(одной командой), необходимо заменить <name_wallet> и <name_moniker> на те, которые вы хотите:
```
dewebd tx staking create-validator \
--chain-id deweb-testnet-sirius \
--commission-rate=0.05 \
--commission-max-rate=0.2 \
--commission-max-change-rate=0.1 \
--min-self-delegation="1000000" \
--amount=1000000udws \
--pubkey $(dewebd tendermint show-validator) \
--moniker "<name_moniker>" \
--from=<name_wallet> \
--gas="auto" \
--fees 555udws
```
#### Проверить логи
```
sudo journalctl -u dewebd -f -o cat
sudo journalctl -fn 100 -u dewebd
```
#### Проверить валидатора
```
dewebd query staking validator <dewebvaloper1...>
dewebd query staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker=="<name_moniker>")' | jq
```
#### Заделегировать себе в стейк
```
dewebd tx staking delegate <dewebvaloper1...> 1000000udws --from <name_wallet> --fees 555udws -y
```
#### Отправить монеты на другой адрес
```
dewebd tx bank send <name_wallet> <deweb1...> 10000000udws --fees 555udws -y
```
#### Чистим данные
```
# удаляем addrbook и очищаем данные
rm $HOME/.deweb/config/addrbook.json
dewebd unsafe-reset-all
# удаляем бинарные файлы, после чего необходимо снова инициализировать ноду
rm -rf ~/.deweb
```
#### Governance
```
dewebd tx gov vote 1 yes --from <name_wallet> --fees 555udws
```
Дискорд проекта
Explorer
