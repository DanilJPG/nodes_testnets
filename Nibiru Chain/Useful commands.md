#### Useful Commands
```Shell
# проверить блоки
nibid status 2>&1 | jq ."SyncInfo"."latest_block_height"

# check the logs
journalctl -u nibid -f -o cat

# check status
curl localhost:26657/status

# check the balance
nibid q bank balances <address>

# collect revards from all validators who were delegated (no commission)
nibid tx distribution withdraw-all-rewards --from <name_wallet> --fees 5000unibi -y

# collect the revards from a separate validator or revards + commission from your validator
nibid tx distribution withdraw-rewards <valoper_address> --from <name_wallet> --fees 5000unibi --commission -y

# to delegate more to the steak (this is how 1 coin is sent)
nibid tx staking delegate <valoper_address> 1000000unibi --from <name_wallet> --fees 5000unibi -y

# redeleting to another validator
nibid tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000unibi --from <name_wallet> --fees 5000unibi -y
```
