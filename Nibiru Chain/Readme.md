## Nibiru Chain

[Discord](https://discord.gg/4sCtbbfc) | [Website](https://nibiru.fi/) | [Faucet](https://discord.com/channels/947911971515293759/984840062871175219)
| --- | --- | ---
```
$request <address_wallet>
```
#### Updating and installing utilities
```
apt update && apt upgrade -y \

apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```

#### Go
```
ver="1.19.1" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

#### Clone the repository and install the binary
```
git clone https://github.com/NibiruChain/nibiru
cd nibiru
git checkout v0.15.0
make install
mv /root/go/bin/nibid /usr/bin/
chmod +x /usr/bin/nibid
nibid version
```

#### Initializing
```
nibid init <moniker-name> --chain-id=nibiru-testnet-1 --home $HOME/.nibid \
nibid config chain-id nibiru-testnet-1
```


#### Download genesis and address book
```
curl -s https://rpc.testnet-1.nibiru.fi/genesis | jq -r .result.genesis > genesis.json
mv genesis.json $HOME/.nibid/config/genesis.json

wget -O $HOME/.nibid/config/addrbook.json "http://65.108.6.45:8000/nibiru/addrbook.json"
```


#### Filling out config.toml
```
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025unibi\"/;" ~/.nibid/config/app.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.nibid/config/config.toml

seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.nibid/config/config.toml

peers="37713248f21c37a2f022fbbb7228f02862224190@35.243.130.198:26656,ff59bff2d8b8fb6114191af7063e92a9dd637bd9@35.185.114.96:26656,cb431d789fe4c3f94873b0769cb4fce5143daf97@35.227.113.63:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.nibid/config/config.toml

CONFIG_TOML="$HOME/.nibid/config/config.toml"
 sed -i 's/timeout_propose =.*/timeout_propose = "100ms"/g' $CONFIG_TOML
 sed -i 's/timeout_propose_delta =.*/timeout_propose_delta = "500ms"/g' $CONFIG_TOML
 sed -i 's/timeout_prevote =.*/timeout_prevote = "100ms"/g' $CONFIG_TOML
 sed -i 's/timeout_prevote_delta =.*/timeout_prevote_delta = "500ms"/g' $CONFIG_TOML
 sed -i 's/timeout_precommit =.*/timeout_precommit = "100ms"/g' $CONFIG_TOML
 sed -i 's/timeout_precommit_delta =.*/timeout_precommit_delta = "500ms"/g' $CONFIG_TOML
 sed -i 's/timeout_commit =.*/timeout_commit = "1s"/g' $CONFIG_TOML
 sed -i 's/skip_timeout_commit =.*/skip_timeout_commit = false/g' $CONFIG_TOML
```


#### Create a service file
```
sudo tee /etc/systemd/system/nibid.service > /dev/null <<EOF
[Unit]
Description=nibid
After=network-online.target

[Service]
User=$USER
ExecStart=$(which nibid) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```


#### Launch
```
systemctl daemon-reload
systemctl start nibid
systemctl enable nibid
sudo journalctl -u nibid -f --no-hostname -o cat
```
#### State Sync
```
sudo systemctl stop nibid

cp $HOME/.nibid/data/priv_validator_state.json $HOME/.nibid/priv_validator_state.json.backup
nibid tendermint unsafe-reset-all --home $HOME/.nibid --keep-addr-book

SNAP_RPC="5.161.99.245:36657"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers=""4372060d7b7268818944a3697fbad1897152ce0d@5.161.99.245:26656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.nibid/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.nibid/config/config.toml

mv $HOME/.nibid/priv_validator_state.json.backup $HOME/.nibid/data/priv_validator_state.json

sudo systemctl restart nibid
sudo journalctl -u nibid -f --no-hostname -o cat

```
#### Wallet
```
# создать кошелек
nibid keys add <name_wallet> --keyring-backend os

# восстановить кошелек (после команды вставить seed)
nibid keys add <name_wallet> --recover --keyring-backend os
```


#### Creating a validator
```
nibid tx staking create-validator \
--chain-id nibiru-testnet-1 \
--commission-rate 0.05 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.1 \
--min-self-delegation "1000000" \
--amount 1000000unibi \
--pubkey $(nibid tendermint show-validator) \
--moniker "<name_moniker>" \
--from <name_wallet> \
--fees 5000unibi
```


#### Useful Commands
```
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

#### Delete
```
systemctl stop nibid && \
systemctl disable nibid && \
rm /etc/systemd/system/nibid.service && \
systemctl daemon-reload && \
cd $HOME && \
rm -rf .nibid nibiru && \
rm -rf $(which nibid)
```

#### If there is a problem connecting peers or genesis, try resetting the network and start with the genesis download step
```
systemctl stop nibid
rm $HOME/.nibid/config/addrbook.json
nibid tendermint unsafe-reset-all --home $HOME/.nibid --keep-addr-book
```
