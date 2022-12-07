### Useful Commands

```Shell
# check the blocks
nibid status 2>&1 | jq ."SyncInfo"."latest_block_height"

# check the logs
journalctl -u nibid -f -o cat

# check status
curl localhost:26657/status

# check the balance
nibid q bank balances <address>

```
#### Useful commands for validator and delegator
# collect rewards from all validators that you have delegated to (without commission)
nibid tx distribution withdraw-all-rewards --from <name_wallet> --fees 5000unibi -y

# collect revards from a separate validator or collect revards + commission from your validator
nibid tx distribution withdraw-rewards <valoper_address> --from <name_wallet> --fees 5000unibi --commission -y

# to allocate more to the steak (this is how 1 coin is sent)
nibid tx staking delegate <valoper_address> 1000000unibi --from <name_wallet> --fees 5000unibi -y

# redeleting to another validator
nibid tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000unibi --from <name_wallet> --fees 5000unibi -y

# unbond 
nibid tx staking unbond <addr_valoper> 1000000unibi --from <name_wallet> --fees 5000unibi -y

# send coins to another address
nibid tx bank send <name_wallet> <address> 1000000unibi --fees 5000unibi -y

# get out of jail
nibid tx slashing unjail --from <name_wallet> --fees 5000unibi -y
```
#### Proposal
  ```
 # список proposals
nibid q gov proposals

# посмотреть результат голосования
nibid q gov proposals --voter <ADDRESS>

# проголосовать за предложение 
nibid tx gov vote 1 yes --from <name_wallet> --fees 555unibi

# внести депозит в предложение
nibid tx gov deposit 1 1000000unibi --from <name_wallet> --fees 555unibi
  ```
#### Edit validator
  ```
  BINARY tx staking edit-validator \
  --chain-id "CHAIN_NAME" \
  --moniker "MONIKER" \
  --identity "IDENTITY" \
  --details "DETAILS" \
  --from "WALLET_NAME"
  ```
