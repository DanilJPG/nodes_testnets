### Useful Commands
```
# проверить блоки
humansd status 2>&1 | jq ."SyncInfo"."latest_block_height"

# проверить логи
journalctl -u humansd -f -o cat

# проверить статус
curl localhost:26657/status

# проверить баланс
humansd q bank balances <address>
```

#### Useful commands for validator and delegator
```
# собрать реварды со всех валидаторов, которым делегировали (без комиссии)
humansd tx distribution withdraw-all-rewards --from <name_wallet> --fees 50000uheart -y

# собрать реварды c отдельного валидатора или реварды + комиссию со своего валидатора
humansd tx distribution withdraw-rewards <valoper_address> --from <name_wallet> --fees 5000uheart --commission -y

# заделегировать себе в стейк еще (так отправляется 1 монетa)
humansd tx staking delegate <valoper_address> 1000000uheart --from <name_wallet> --fees 5000uheart -y

# ределегирование на другого валидатора
humansd tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000uheart --from <name_wallet> --fees 5000uheart -y

# unbond 
humansd tx staking unbond <addr_valoper> 1000000uheart --from <name_wallet> --fees 5000uheart -y

# отправить монеты на другой адрес
humansd tx bank send <name_wallet> <address> 1000000uheart --fees 5000uheart -y

# выбраться из тюрьмы
humansd tx slashing unjail --from <name_wallet> --fees 5000uheart -y
```

#### Proposal
```
# список proposals
humansd q gov proposals

# посмотреть результат голосования
humansd q gov proposals --voter <ADDRESS>

# проголосовать за предложение 
humansd tx gov vote 1 yes --from <name_wallet> --fees 555uheart

# внести депозит в предложение
humansd tx gov deposit 1 1000000uheart --from <name_wallet> --fees 555uheart
```
#### Change ports
```
# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:36658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:36657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:6061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:36656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":36660\"%" $HOME/.humans/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:9190\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:9191\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1327\"%" $HOME/.humans/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:36657\"%" $HOME/.humans/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:36656\"/" $HOME/.humans/config/config.toml
```

#### Modify Validator
```
humansd tx staking edit-validator \
  --chain-id "testnet-1" \
  --moniker "MONIKER" \
  --identity "IDENTITY" \
  --details "DETAILS" \
  --from "WALLET_NAME"
```  
