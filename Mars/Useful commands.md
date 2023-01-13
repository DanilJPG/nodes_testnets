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
```
# collect rewards from all validators that you have delegated to (without commission)
nibid tx distribution withdraw-all-rewards --from <name_wallet> --fees 5000umars -y

# collect revards from a separate validator or collect revards + commission from your validator
nibid tx distribution withdraw-rewards <valoper_address> --from <name_wallet> --fees 5000umars --commission -y

# to allocate more to the steak (this is how 1 coin is sent)
nibid tx staking delegate <valoper_address> 1000000umars --from <name_wallet> --fees 5000umars -y

# redeleting to another validator
marsd tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000umars --from <name_wallet> --fees 5000umars -y

# unbond 
marsd tx staking unbond <addr_valoper> 1000000umars --from <name_wallet> --fees 5000umars -y

# send coins to another address
marsd tx bank send <name_wallet> <address> 1000000umars --fees 5000umars -y

# get out of jail
marsd tx slashing unjail --from <name_wallet> --fees 5000umars -y
```
#### Proposal
  ```
 # list of proposals
marsd q gov proposals

# to see the result of the vote
marsd q gov proposals --voter <ADDRESS>

# vote for proposal 
marsd tx gov vote 1 yes --from <name_wallet> --fees 555umars

# make a deposit to the proposal
marsd tx gov deposit 1 1000000umars --from <name_wallet> --fees 555umars
  ```
#### Edit validator
  ```
  marsd tx staking edit-validator \
  --chain-id "CHAIN_NAME" \
  --moniker "MONIKER" \
  --identity "IDENTITY" \
  --details "DETAILS" \
  --from "WALLET_NAME"
  ```
