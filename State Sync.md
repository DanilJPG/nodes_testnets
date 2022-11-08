## State sync
#### НА СЕРВЕРЕ ДОНОРЕ
```
Открываем доступ RPC nano /root/.<bin>/config/config.toml

laddr = "tcp://0.0.0.0:26657"
Открываем app.toml и настраиваем снэпшоты nano $HOME/.defund/config/app.toml

# при настроенном прунинге 100/1000/10 snapshot-interval ставим не меньше 1000
snapshot-interval = 1000
snapshot-keep-recent = 2
Собираем данные (ID ноды +ip +порт)

<bin> status

# id":"f7aa6ffae725764f473b0d5627a664d3e3cd6164","listen_addr":"65.144.108.99:26656"
```
Example:
![image](https://user-images.githubusercontent.com/57448493/200609885-b30d9696-0837-4d96-8360-e2e492e36ab9.png)
```
# Получаем примерно такое f7aa6ffae725764f473b0d5627a664d3e3cd6164@65.144.108.99:26656
Если нода работает перезагружаем ее 

sudo systemctl restart <bin> && sudo journalctl -u <bin> -f -o cat
Смотрим свой IP echo http://$(wget -qO- eth0.me):26657/

В браузере проверяем открылся ли доступ http://65.144.108.99:26657/
```

#### НА НОВОМ СЕРВЕРЕ
```
Ставим ПО и начинаем настраиваем persistent_peers
$HOME/.<bin>/config/config.toml

peers="f7aa6ffae725764f473b0d5627a664d3e3cd6164@65.144.108.99:26656" 
sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" ~/.<bin>/config/config.toml

# persistent_peers = "f7aa6ffae725764f473b0d5627a664d3e3cd6164@65.144.108.99:26656"
Вводим команды по очереди

SNAP_RPC=65.144.108.99:26657
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" ~/.<bin>/config/config.toml
<bin> tendermint unsafe-reset-all
<bin> start
# <bin> unsafe-reset-all
# <bin> tendermint unsafe-reset-all
```
Если нода не видит снап и не цепляется с ошибкой err="auth failure: secret conn failed, то перезагружаем донора

После того, как  нода синхронизировалась останавливаем ее, ставим сервисник и перезапускаем
