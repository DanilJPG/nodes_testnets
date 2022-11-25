## Celestia

### Installing a complete assembly
[Website](https://celestia.org/) | [Docs](https://docs.celestia.org/) | [Explorers](https://celestia.explorers.guru/) | [Github](https://github.com/celestiaorg) | [Discord](https://discord.gg/celestiacommunity)
| --- | --- | --- | --- | ---

#### Updating and installing "keys" and utilities
```
sudo apt update && sudo apt upgrade -y \
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu -y
```
#### Installing Go
```
cd $HOME
ver="1.19.1"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile

go version
```
#### Installing the Celestia App
```
cd $HOME
rm -rf celestia-app
git clone https://github.com/celestiaorg/celestia-app.git
cd celestia-app
git checkout v0.6.0
make install
```
```
celestia-appd version
```
```
git clone https://github.com/celestiaorg/networks
```
#### Installing a node
```
cd $HOME
rm -rf networks
git clone https://github.com/celestiaorg/networks.git
```
#### set the variables
```
CELESTIA_NODENAME="<name_operator>"
CELESTIA_WALLET="<name_wallet>"

CELESTIA_CHAIN="mamaki
```
```
echo 'export CELESTIA_CHAIN='$CELESTIA_CHAIN >> $HOME/.bash_profile
echo 'export CELESTIA_NODENAME='${CELESTIA_NODENAME} >> $HOME/.bash_profile
echo 'export CELESTIA_WALLET='${CELESTIA_WALLET} >> $HOME/.bash_profile
source $HOME/.bash_profile
```
#### Initializing
```
celestia-appd init $CELESTIA_NODENAME --chain-id $CELESTIA_CHAIN 
```
```
cp $HOME/networks/mamaki/genesis.json $HOME/.celestia-app/config/
```
#### Setting up the client
```
celestia-appd config chain-id $CELESTIA_CHAIN
celestia-appd config keyring-backend test
```
#### Correct the configuration file
```
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025ujkl\"/;" ~/.celestia-app/config/app.toml
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.celestia-app/config/config.toml

PEERS=$(curl -sL https://raw.githubusercontent.com/celestiaorg/networks/master/mamaki/bootstrap-peers.txt | tr -d '\n')
sed -i.bak -e "s/^persistent-peers *=.*/persistent-peers = \"$peers\"/" $HOME/.celestia-app/config/config.toml

sed -i 's/timeout-commit = ".*/timeout-commit = "25s"/g' $HOME/.celestia-app/config/config.toml
sed -i 's/peer-gossip-sleep-duration *=.*/peer-gossip-sleep-duration = "2ms"/g' $HOME/.celestia-app/config/config.toml
sed -i 's/timeout-commit = ".*/timeout-commit = "25s"/g' $HOME/.celestia-app/config/config.toml
sed -i 's/peer-gossip-sleep-duration *=.*/peer-gossip-sleep-duration = "2ms"/g' $HOME/.celestia-app/config/config.toml
```
#### Service file
```
sudo tee $HOME/celestia-appd.service > /dev/null <<EOF
[Unit]
  Description=celestia-appd Cosmos daemon
  After=network-online.target
[Service]
  User=$USER
  ExecStart=$(which celestia-appd) start
  Restart=on-failure
  RestartSec=3
  LimitNOFILE=65535
[Install]
  WantedBy=multi-user.target
EOF

sudo mv $HOME/celestia-appd.service /etc/systemd/system/
```
#### Launch
```
sudo systemctl enable celestia-appd
sudo systemctl daemon-reload
sudo systemctl restart celestia-appd && journalctl -u celestia-appd -f -o cat
```
#### Log checking and synchronization
```
# check the blocks
celestia-appd status 2>&1 | jq ."SyncInfo"."latest_block_height"

# check the logs
sudo journalctl -u celestia-appd -f -o cat

# check status
curl localhost:26657/status
```
#### Wallet
```
# create a wallet
celestia-appd keys add <name_wallet> --keyring-backend os

# restore the wallet (after the command insert seed)
celestia-appd keys add <name_wallet> --recover --keyring-backend os
```
#### Prinning
```
PRUNING="custom"
PRUNING_KEEP_RECENT="100"
PRUNING_INTERVAL="10"

sed -i -e "s/^pruning *=.*/pruning = \"$PRUNING\"/" $HOME/.celestia-app/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \
\"$PRUNING_KEEP_RECENT\"/" $HOME/.celestia-app/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \
\"$PRUNING_INTERVAL\"/" $HOME/.celestia-app/config/app.toml
```
#### Validator
```
celestia-appd tx staking create-validator \
--chain-id mamaki \
--commission-rate 0.05 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.1 \
--min-self-delegation "1000000" \
--amount 1000000utia \
--pubkey $(celestia-appd tendermint show-validator) \
--moniker "<name_moniker>" \
--from <name_wallet> \
--fees 5550utia
```
#### Useful Commands
```
# check the blocks
celestia-appd status 2>&1 | jq ."SyncInfo"."latest_block_height"

# check the logs
sudo journalctl -u celestia-appd -f -o cat

# check status
curl localhost:26657/status

# check the balance
celestia-appd q bank balances <address>

# display a list of wallets
celestia-appd keys list

# Show account key
celestia-appd keys show <name_wallet> --bech acc
```
#### Операции с валидатором
```
# collect commissions + revards
celestia-appd tx distribution withdraw-rewards <valoper_address> --from <name_wallet> --fees 5555utia --commission -y

# to delegate more to the steak (this is how 1 coin is sent)
celestia-appd tx staking delegate <valoper_address> 1000000utia --from <name_wallet> --fees 5555utia -y

# redeleting to another validator
celestia-appd tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000utia --from <name_wallet> --fees 5555utia -y

# unbond 
celestia-appd tx staking unbond <addr_valoper> 1000000utia --from <name_wallet> --fees 5555utia -y

# send coins to another address
celestia-appd tx bank send <name_wallet> <address> 1000000utia --fees 5555utia -y

# get out of jail
celestia-appd tx slashing unjail --from <name_wallet> --fees 5555utia -y
```
#### Update
```
sudo systemctl stop celestia-appd && cd celestia-app
git pull
git checkout <>
make install
celestia-appd version
```
#### Delete
```
sudo systemctl stop celestia-appd && \
sudo systemctl disable celestia-appd && \
rm /etc/systemd/system/celestia-appd.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf .celestia-app celestia-app && \
rm -rf $(which celestia-appd)
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
