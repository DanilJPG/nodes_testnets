### Useful Commands
```
# проверить блоки
dymd status 2>&1 | jq ."SyncInfo"."latest_block_height"

# проверить логи
journalctl -u dymd -f -o cat

# проверить статус
curl localhost:26657/status

# проверить баланс
dymd q bank balances <address>

# проверить pubkey валидатора
dymd tendermint show-validator

# проверить валидатора
dymd query staking validator <valoper_address>
dymd query staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker=="<name_moniker>")' | jq

# проверка информации по TX_HASH
dymd query tx <TX_HASH>
```

#### Useful commands for validator and delegator
```
# собрать реварды со всех валидаторов, которым делегировали (без комиссии)
dymd tx distribution withdraw-all-rewards --from <name_wallet> --fees 5000udym -y

# собрать реварды c отдельного валидатора или реварды + комиссию со своего валидатора
dymd tx distribution withdraw-rewards <valoper_address> --from <name_wallet> --fees 5000udym --commission -y

# заделегировать себе в стейк еще (так отправляется 1 монетa)
dymd tx staking delegate <valoper_address> 1000000udym --from <name_wallet> --fees 5000udym -y

# ределегирование на другого валидатора
dymd tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000udym --from <name_wallet> --fees 5000udym -y

# unbond 
dymd tx staking unbond <addr_valoper> 1000000udym --from <name_wallet> --fees 5000udym -y

# отправить монеты на другой адрес
dymd tx bank send <name_wallet> <address> 1000000udym --fees 5000udym -y

# выбраться из тюрьмы
dymd tx slashing unjail --from <name_wallet> --fees 5000udym -y
```

#### Proposal
```
# список proposals
dymd q gov proposals

# посмотреть результат голосования
dymd q gov proposals --voter <ADDRESS>

# проголосовать за предложение 
dymd tx gov vote 1 yes --from <name_wallet> --fees 555udym

# внести депозит в предложение
dymd tx gov deposit 1 1000000udym --from <name_wallet> --fees 555udym

# создать предложение
dymd tx gov submit-proposal --title="Randomly reward" --description="Reward 10 testnet participants who completed more than 3 tasks" --type="Text" --deposit="11000000grain" --from=<name_wallet> --fees 500udym
```
#### Change ports
# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:36658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:36657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:6061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:36656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":36660\"%" $HOME/.dymension/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:9190\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:9191\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1327\"%" $HOME/.dymension/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:36657\"%" $HOME/.dymension/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:36656\"/" $HOME/.dymension/config/config.toml
