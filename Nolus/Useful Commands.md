#### Useful Commands
```Shell
# check the blocks
nolusd status 2>&1 | jq ."SyncInfo"."latest_block_height"

# check the logs
journalctl -u nolusd -f -o cat

# check status
curl localhost:26657/status

# check the balance
nolusd q bank balances <address>

# check the validator's pubkey
nolusd tendermint show-validator
```
#### For validator
```Shell
# collect revards from all validators who were delegated (no commission)
nolusd tx distribution withdraw-all-rewards --from $WALLET --fees 5000unls -y

# collect the revards from a separate validator or revards + commission from your validator
nolusd tx distribution withdraw-rewards <valoper_address> --from $WALLET --fees 5000unls --commission -y

# to delegate more to the steak (this is how 1 coin is sent)
nolusd tx staking delegate <valoper_address> 1000000umpwr --from $WALLET --fees 5000unls -y

# redeleting to another validator
nolusd tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000umpwr --from $WALLET --fees 5000unls -y

# unbond 
nolusd tx staking unbond <addr_valoper> 1000000unls --from $WALLET --fees 5000unls -y

# send coins to another address
nolusd tx bank send $WALLET <address> 1000000unls --fees 5000unls -y
```
#### Proposal
```Shell
# list of proposals
nolusd q gov proposals

# see the voting results
nolusd q gov proposals --voter <ADDRESS>

# vote for the proposal 
nolusd tx gov vote 1 yes --from <name_wallet> --fees 555unls
```

#### Edit validator 
```Shell
nolusd tx staking edit-validator \
  --chain-id "CHAIN_NAME" \
  --moniker "MONIKER" \
  --identity "IDENTITY" \
  --details "DETAILS" \
  --from "WALLET_NAME"
```
