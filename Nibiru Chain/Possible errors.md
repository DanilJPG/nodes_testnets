#### Error: rpc error: code = NotFound desc = rpc error: code = NotFound desc = account <addr_wallet> not found: key not found
```
Ошибка связана с переменной,попробуйте проверить адрес в эксплорере,либо восстановить кошелек. Если ошибка связана с адресом валидатора,то проверьте синрхронизацию и правильность привязки кошелька к валидатору(проверьте имя оператора и имя кошелька при создании валдитора)
The error is related to the variable, try to check the address in the explorer, or restore the purse. If the error is related to the validator address, then check the synchronization and correct binding purse to the validator (check the operator name and purse name when creating a validator)

# создать кошелек - create a wallet
nibid keys add <name_wallet> --keyring-backend os

# восстановить кошелек (после команды вставить seed) - restore the wallet (after the command insert seed)
nibid keys add <name_wallet> --recover --keyring-backend os

```
#### Error: rpc error: code = Unknown desc = unknown query path: unknown request
```

```

#### Error: rpc error: code = Unknown desc = account sequence mismatch, expected 11, got 8: incorrect account sequence: unknown request
```
Допишите в конец вашей команды -S <число>, в моем случае это 11
Add -S <number> to the end of your command, in my case it is 11
Example:
nibid tx staking delegate <valoper> 9000000unibi --from wallet --chain-id altruistic-1 --gas-prices 0.1unibi --gas-adjustment 1.5 --gas auto -s 11 -y
```

#### Error: rpc error: code = Unknown desc = runtime error: invalid memory address or nil pointer dereference: panic
```

```

#### Error: invalid character 'o' looking for beginning of value
```

```

#### parse error: Invalid numeric literal at line `<number>` , column `<number>`
```

```

#### Error: invalid Bech32 prefix; expected nibivaloper, got nibi
Usage: nibid tx staking delegate [validator-addr] [amount] [flags]
```

```

#### Error: post failed: Post "http://localhost:26657/": dial tcp 127.0.0.1:26657: connect: connection refused
```

```

#### 
