### Useful Commands
```
# проверить блоки
andromedad status 2>&1 | jq ."SyncInfo"."latest_block_height"

# проверить логи
journalctl -u andromedad -f -o cat

# проверить статус
curl localhost:26657/status

# проверить баланс
andromedad q bank balances <address>
```

#### Useful commands for validator and delegator
```
# собрать реварды со всех валидаторов, которым делегировали (без комиссии)
andromedad tx distribution withdraw-all-rewards --from <name_wallet> --fees 5000uandr -y

# собрать реварды c отдельного валидатора или реварды + комиссию со своего валидатора
andromedad tx distribution withdraw-rewards <valoper_address> --from <name_wallet> --fees 5000uandr --commission -y

# заделегировать себе в стейк еще (так отправляется 1 монетa)
andromedad tx staking delegate <valoper_address> 1000000uandr --from <name_wallet> --fees 5000uandr -y

# ределегирование на другого валидатора
andromedad tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000uandr --from <name_wallet> --fees 5000uandr -y

# unbond 
andromedad tx staking unbond <addr_valoper> 1000000uandr --from <name_wallet> --fees 5000uandr -y

# отправить монеты на другой адрес
andromedad tx bank send <name_wallet> <address> 1000000uandr --fees 5000uandr -y

# выбраться из тюрьмы
andromedad tx slashing unjail --from <name_wallet> --fees 5000uandr -y
```

#### Proposal
```
# список proposals
andromedad q gov proposals

# посмотреть результат голосования
andromedad q gov proposals --voter <ADDRESS>

# проголосовать за предложение 
andromedad tx gov vote 1 yes --from <name_wallet> --fees 555uandr

# внести депозит в предложение
andromedad tx gov deposit 1 1000000uandr --from <name_wallet> --fees 555uandr
```
#### Change ports
```
# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:36658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:36657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:6061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:36656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":36660\"%" $HOME/.andromedad/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:9190\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:9191\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1327\"%" $HOME/.andromedad/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:36657\"%" $HOME/.andromedad/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:36656\"/" $HOME/.andromedad/config/config.toml
```
