#### Useful Commands
```Shell
# check the blocks
empowerd status 2>&1 | jq ."SyncInfo"."latest_block_height"

# check the logs
journalctl -u empowerd -f -o cat

# check status
curl localhost:26657/status

# check the balance
empowerd q bank balances <address>

# check the validator's pubkey
empowerd tendermint show-validator
```
#### For validator
```Shell
# collect revards from all validators who were delegated (no commission)
empowerd tx distribution withdraw-all-rewards --from $WALLET --fees 5000umpwr -y

# collect the revards from a separate validator or revards + commission from your validator
empowerd tx distribution withdraw-rewards <valoper_address> --from $WALLET --fees 5000umpwr --commission -y

# to delegate more to the steak (this is how 1 coin is sent)
empowerd tx staking delegate <valoper_address> 1000000umpwr --from $WALLET --fees 5000umpwr -y

# redeleting to another validator
empowerd tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000umpwr --from $WALLET --fees 5000umpwr -y

# unbond 
empowerd tx staking unbond <addr_valoper> 1000000umpwr --from $WALLET --fees 5000umpwr -y

# send coins to another address
empowerd tx bank send $WALLET <address> 1000000umpwr --fees 5000umpwr -y
```
#### Proposal
```Shell
# list of proposals
empowerd q gov proposals

# see the voting results
empowerd q gov proposals --voter <ADDRESS>

# vote for the proposal 
empowerd tx gov vote 1 yes --from <name_wallet> --fees 555umpwr
```

#### Edit validator 
```Shell
BINARY tx staking edit-validator \
  --chain-id "CHAIN_NAME" \
  --moniker "MONIKER" \
  --identity "IDENTITY" \
  --details "DETAILS" \
  --from "WALLET_NAME"
```
