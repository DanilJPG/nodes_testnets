#### Useful Commands
```
# check the blocks
lambdavm status 2>&1 | jq ."SyncInfo"."latest_block_height"

# check the logs
journalctl -u lambdavm -f -o cat

# check status
curl localhost:26657/status

# check the balance
lambdavm q bank balances <address>

# check the validator's pubkey
empowerd tendermint show-validator
```

#### Transactions
```
# collect revards from all validators who were delegated (no commission)
lambdavm tx distribution withdraw-all-rewards --from <name_wallet> --fees 5000ulamb -y

# collect the revards from a separate validator or revards + commission from your validator
lambdavm tx distribution withdraw-rewards <valoper_address> --from <name_wallet> --fees 5000ulamb --commission -y

# to delegate more to the steak (this is how 1 coin is sent)
lambdavm tx staking delegate <valoper_address> 1000000ulamb --from <name_wallet> --fees 5000ulamb -y

# redeleting to another validator
lambdavm tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000ulamb --from <name_wallet> --fees 5000ulamb -y

# unbond 
lambdavm tx staking unbond <addr_valoper> 1000000ulamb --from <name_wallet> --fees 5000ulamb -y

# send coins to another address
lambdavm tx bank send <name_wallet> <address> 1000000ulamb --fees 5000umpwr -y
```
