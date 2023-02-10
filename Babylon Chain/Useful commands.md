### Useful Commands
```
# проверить блоки
babylond status 2>&1 | jq ."SyncInfo"."latest_block_height"

# проверить логи
journalctl -u babylond -f -o cat

# проверить статус
curl localhost:26657/status

# проверить баланс
babylond q bank balances <address>
```

#### Useful commands for validator and delegator
```
# собрать реварды со всех валидаторов, которым делегировали (без комиссии)
babylond tx distribution withdraw-all-rewards --from <name_wallet> --fees 5000ubbn -y

# собрать реварды c отдельного валидатора или реварды + комиссию со своего валидатора
babylond tx distribution withdraw-rewards <valoper_address> --from <name_wallet> --fees 5000ubbn --commission -y

# заделегировать себе в стейк еще (так отправляется 1 монетa)
babylond tx staking delegate <valoper_address> 1000000ubbn --from <name_wallet> --fees 5000ubbn -y

# ределегирование на другого валидатора
babylond tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000ubbn --from <name_wallet> --fees 5000ubbn -y

# unbond 
babylond tx staking unbond <addr_valoper> 1000000ubbn --from <name_wallet> --fees 5000ubbn -y

# отправить монеты на другой адрес
babylond tx bank send <name_wallet> <address> 1000000ubbn --fees 5000ubbn -y

# выбраться из тюрьмы
babylond tx slashing unjail --from <name_wallet> --fees 5000ubbn -y
```

#### Proposal
```
# список proposals
babylond q gov proposals

# посмотреть результат голосования
babylond q gov proposals --voter <ADDRESS>

# проголосовать за предложение 
babylond tx gov vote 1 yes --from <name_wallet> --fees 555ubbn

# внести депозит в предложение
babylond tx gov deposit 1 1000000ubbn --from <name_wallet> --fees 555ubbn
```
#### Change ports
```
# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:36658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:36657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:6061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:36656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":36660\"%" $HOME/.babylond/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:9190\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:9191\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1327\"%" $HOME/.babylond/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:36657\"%" $HOME/.babylond/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:36656\"/" $HOME/.babylond/config/config.toml
```
