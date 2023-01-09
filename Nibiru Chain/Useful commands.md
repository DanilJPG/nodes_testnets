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
 # list of proposals
nibid q gov proposals

# to see the result of the vote
nibid q gov proposals --voter <ADDRESS>

# vote for proposal 
nibid tx gov vote 1 yes --from <name_wallet> --fees 555unibi

# make a deposit to the proposal
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

#### Change ports
```
# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:36658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:36657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:6061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:36656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":36660\"%" $HOME/.lava/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:9190\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:9191\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1327\"%" $HOME/.lava/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:36657\"%" $HOME/.lava/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:36656\"/" $HOME/.lava/config/config.toml
```
