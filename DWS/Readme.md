## DWS

[Discord](https://discord.gg/dws) | [Website](https://deweb.services/) 

### Sirius Testnet

DWS offers a complete set of infrastructure and application services that enable you to run virtually everything from enterprise applications and big data projects to social games and mobile applications in the decentralized cloud.

dewebd is a blockchain application created using Cosmos SDK v.0.45.0 and Tendermint v.0.34.15.

You can run the validator program using a binary file or by compiling it yourself.

Recommended server modifications: 4 cores, 8 RAM, 200 ssd;

Step 0A - Run the full node/validator using binaries
Download the latest version (v0.2) of the binary from Github:

```
#### Updating repositories
sudo apt update && sudo apt upgrade -y

#### Installing the necessary utilities
sudo apt install curl build-essential git wget jq make gcc tmux nvme-cli -y
```

For further interaction with the program you will need to install Golang (one command)

```
wget https://golang.org/dl/go1.18.1.linux-amd64.tar.gz; \
rm -rv /usr/local/go; \
tar -C /usr/local -xzf go1.18.1.linux-amd64.tar.gz && \
rm -v go1.18.1.linux-amd64.tar.gz && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile && \
source ~/.bash_profile && \
go version
```

### Node Installation
```
git clone https://github.com/deweb-services/deweb.git && cd deweb
git checkout v0.3.1
make install
проверьте версию: dewebd version 
0.3.1
```

#### Initializing a node
Instead of <name_node> insert the name of the node you want to assign
```
dewebd init <name_node> --chain-id deweb-testnet-sirius
```
#### Download Genesis
```
wget -O $HOME/.deweb/config/genesis.json "https://raw.githubusercontent.com/deweb-services/deweb/main/genesis.json"
```

```
Version Verification:
sha256sum ~/.deweb/config/genesis.json 

5316dc5abf1bc46813b673e920cb6faac06850c4996da28d343120ee0d713ab9  /root/.deweb/config/genesis.json
```

#### Setting up a node (with different commands)
```
cd $HOME
# setting up piers
peers="F4C24583640A7BE659F8A6FA734F9ED61FB10CD9@78.137.4.44:42656" sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/; s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.deweb/config/config.toml

#setting up the seat
sed -E -i 's/seeds = \".*\"/seeds = \"74d8f92c37ffe4c6393b3718ca531da8f0bf0594@seed1.deweb.services:26656\"/' $HOME/.deweb/config/config.toml

#gas charge
sed -E -i 's/minimum-gas-prices = \".*\"/minimum-gas-prices = \"0.001udws\"/' $HOME/.deweb/config/app.toml

###opening a port
sudo ufw allow 26656
```

#### Create a service file
```
sudo tee /etc/systemd/system/dewebd.service > /dev/null <<EOF
echo "[Unit]
Description=DWS Node
After=network-online.target
[Service]
User=${USER}
ExecStart=$(which dewebd) start
Restart=always
RestartSec=3
LimitNOFILE=4096
[Install] 
WantedBy=multi-user.target 
EOF
```
#### Launch
```
sudo systemctl daemon-reload && \
sudo systemctl enable dewebd && \
sudo systemctl restart dewebd && sudo journalctl -u dewebd -f -o cat
```
If there are errors related to the connection of peers, it is worth asking for help in the discord, and in the bookmarks to look for the right information

https://discord.gg/Kyuc3cvx

#### Checking synchronization and blocks
```
dewebd status 2>&1 | jq ."SyncInfo"."latest_block_height"
curl localhost:26657/status
```
While synchronization is going on, we can create and replenish the wallet:
```
#create a wallet
dewebd keys add <name_wallet>

#restore the wallet (after the command insert seed)
dewebd keys add <name_wallet> --recover

#check the balance
dewebd q bank balances <deweb1...>
Если не может подключиться к пирам,то проверяйте: в дискорде проекта имеется канал peers
```
If you change something that needs to be changed, then refer to the node stop:
```
#Stop the node, delete the address book and reset the data
sudo systemctl stop dewebd
rm $HOME/.deweb/config/addrbook.json
dewebd unsafe-reset-all

#restart the node
sudo systemctl restart dewebd && journalctl -u dewebd -f -o cat
After synchronization, go to discord asking for tokens (channel #faucet), 1 dws = 1000000 udws
```
#### State Sync 
```
sudo systemctl stop dewebd

cp $HOME/.deweb/data/priv_validator_state.json $HOME/.deweb/priv_validator_state.json.backup
dewebd tendermint unsafe-reset-all --home $HOME/.deweb --keep-addr-book

SNAP_RPC="https://dws-testnet.nodejumper.io:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="c5b45045b0555c439d94f4d81a5ec4d1a578f98c@dws-testnet.nodejumper.io:27656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.deweb/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.deweb/config/config.toml

mv $HOME/.deweb/priv_validator_state.json.backup $HOME/.deweb/data/priv_validator_state.json

sudo systemctl restart dewebd
sudo journalctl -u dewebd -f --no-hostname -o cat
```
#### Set the validator (one command), you must replace <name_wallet> and <name_moniker> on the ones you want:
```
dewebd tx staking create-validator \
--chain-id deweb-testnet-sirius \
--commission-rate=0.05 \
--commission-max-rate=0.2 \
--commission-max-change-rate=0.1 \
--min-self-delegation="1000000" \
--amount=1000000udws \
--pubkey $(dewebd tendermint show-validator) \
--moniker "<name_moniker>" \
--from=<name_wallet> \
--gas="auto" \
--fees 555udws
```
#### Check the logs
```
sudo journalctl -u dewebd -f -o cat
sudo journalctl -fn 100 -u dewebd
```
#### Check the validator
```
dewebd query staking validator <dewebvaloper1...>
dewebd query staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker=="<name_moniker>")' | jq
```
#### To make it into a steak.
```
dewebd tx staking delegate <dewebvaloper1...> 1000000udws --from <name_wallet> --fees 555udws -y
```
#### Send coins to another address
```
dewebd tx bank send <name_wallet> <deweb1...> 10000000udws --fees 555udws -y
```
#### Clearing the data
```
# Delete addrbook and clear the data
rm $HOME/.deweb/config/addrbook.json
dewebd unsafe-reset-all

# Delete the binary files, after which you must reinitialize the node
rm -rf ~/.deweb
```
#### Governance
```
dewebd tx gov vote 1 yes --from <name_wallet> --fees 555udws
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
