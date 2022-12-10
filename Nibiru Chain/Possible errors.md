#### Error: rpc error: code = NotFound desc = rpc error: code = NotFound desc = account <addr_wallet> not found: key not found
```Shell
Ошибка связана с переменной,попробуйте проверить адрес в эксплорере,либо восстановить кошелек. Если ошибка связана с адресом валидатора,то проверьте синрхронизацию и правильность привязки кошелька к валидатору(проверьте имя оператора и имя кошелька при создании валдитора)
The error is related to the variable, try to check the address in the explorer, or restore the purse. If the error is related to the validator address, then check the synchronization and correct binding purse to the validator (check the operator name and purse name when creating a validator)

# создать кошелек - create a wallet
nibid keys add <name_wallet> --keyring-backend os

# восстановить кошелек (после команды вставить seed) - restore the wallet (after the command insert seed)
nibid keys add <name_wallet> --recover --keyring-backend os

если не помогло,проверьте синхронизацию узла
If it does not help, check the synchronization of the node
nibid status | jq .SyncInfo.catching_up
```
#### Error: rpc error: code = Unknown desc = unknown query path: unknown request
```Shell
Проверьте правильность введенной команды
Check if the command is correct
```

#### Error: rpc error: code = Unknown desc = account sequence mismatch, expected 11, got 8: incorrect account sequence: unknown request
```Shell
Допишите в конец вашей команды -S <число>, в моем случае это 11
Add -S <number> to the end of your command, in my case it is 11
Example:
nibid tx staking delegate <valoper> 9000000unibi --from wallet --chain-id altruistic-1 --gas-prices 0.1unibi --gas-adjustment 1.5 --gas auto -s 11 -y
```

#### Error: rpc error: code = Unknown desc = runtime error: invalid memory address or nil pointer dereference: panic
```Shell
Ваш бинарный файл не соответствует genesis сети,скачайте другой genesis либо проверьте правильность клонированного репозитория из которого копировали бинарный файл
Your binary does not match the genesis network, download another genesis or check the correctness of the cloned repository from which you copied the binary
```

#### Error: invalid character 'o' looking for beginning of value
```Shell
Проверьте правильность команды, возможно какой-то `0` является буквой `o`
Check if the command is correct, maybe some `0` is the letter `o`.
```

#### Parse error: Invalid numeric literal at line `<number>` , column `<number>`
```Shell
Возможные решения:
- проверьте правильность версии бинарного файла
- проверьте порты 
Possible solutions:
- check the correct version of the binary file
- check the ports 
```

#### Error: invalid Bech32 prefix; expected nibivaloper, got nibi
Usage: nibid tx staking delegate [validator-addr] [amount] [flags]
```Shell
Check that the command is typed correctly
Проверьте правильность набора команды
```


#### Failed to connect to localhost port 26657: Connection refused
```Shell
Попробуйте изменить порт
Try changing the port

# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:36658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:36657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:6061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:36656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":36660\"%" $HOME/.nibid/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:9190\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:9191\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1327\"%" $HOME/.nibid/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:36657\"%" $HOME/.nibid/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:36656\"/" $HOME/.nibid/config/config.toml


Попробуйте найти и используйте правильный State Sync или Snapshot
Try to find and use the correct State Sync or Snapshot
```

#### Error: rpc error: code = Unknown desc = rpc error: code = Unknown desc = failed to execute message; message index: 0: redelegation to this validator already in progress; first redelegation to this validator must complete before next redelegation 
```Shell
Ожидайте и попробуйте заново, и проверьте tx hash в эксплорере
Wait and try again, and check the tx hash in the explorer
```


#### Failed to execute message; message index: 0: validator does not exist
```Shell
Ваш валидатор или валидатор которому делегируете не существует,проверьте правильность использованной команды
Your validator or the validator you delegate does not exist, check if the command you used is correct
```

#### If there is a problem connecting peers or genesis, try resetting the network and start with the genesis download step
```Shell
systemctl stop nibid
rm $HOME/.nibid/config/addrbook.json
nibid tendermint unsafe-reset-all --home $HOME/.nibid --keep-addr-book
```

#### Error: error during handshake: error on replay: validator set is nil in genesis and still empty after InitChain
```
Ваш genesis файл не скачен после инициализации
Your genesis file is not downloaded after initialization
```
