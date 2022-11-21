## Lambda Mainnet

Dicord: https://discord.gg/lambdanetwork

Docs: https://docs.lambda.im/validators/testnet.html

#### Upgrade and install dependencies
```
sudo apt update && sudo apt upgrade -y && \
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```
#### Installing Go
```
ver="1.19.1"
cd $HOME
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
```

#### Cloning a repository 
```
git clone https://github.com/LambdaIM/lambdavm.git
cd lambdavm
make install
```

#### Initializing 
```
lambdavm init <your_custom_moniker> --chain-id lambda_92000-1 \
lambdavm config chain-id lambda_92000-1
```

#### Download genesis
```
wget -O /root/.lambdavm/config//genesis.json "https://raw.githubusercontent.com/LambdaIM/mainnet/main/lambda_92000-1/genesis.json"
#check

`sha256sum genesis.json`
# 1ff02001539bc1e9828fe170006f055c04df280c61c4ca9ecc9e7b6a272b7777  genesis.json
```

#### Correct the configuration file
```
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" /root/.lambdavm/config/config.toml

sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025ulamb\"/;" /root/.lambdavm/config/app.toml

PEERS=`curl -sL https://raw.githubusercontent.com/LambdaIM/mainnet/main/lambda_92000-1/peers.txt | sort -R | head -n 10 | awk '{print $1}' | paste -s -d, -`

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" ~/.lambdavm/config/config.toml

SEEDS=`curl -sL https://raw.githubusercontent.com/LambdaIM/mainnet/main/lambda_92000-1/seeds.txt | awk '{print $1}' | paste -s -d, -`
sed -i.bak -e "s/^seeds =.*/seeds = \"$SEEDS\"/" ~/.lambdavm/config/config.toml
```

#### Create a service file
```
sudo tee /etc/systemd/system/lambdavm.service > /dev/null <<EOF
[Unit]
Description=lambdavm
After=network-online.target
[Service]
User=$USER
ExecStart=$(which lambdavm) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

#### Launch
```
systemctl daemon-reload && \
systemctl enable lambdavm && \
systemctl restart lambdavm && journalctl -u lambdavm -f -o cat
```

#### Creating a validator 
```
lambdavm tx staking create-validator \
--amount 1000000000000000000ulamb \
--pubkey $(lambdavm tendermint show-validator) \
--moniker "garfield" \
--chain-id lambda_92000-1 \
--commission-rate "0.05" \
--min-self-delegation "1000000" \
--commission-max-rate "0.20" \
--commission-max-change-rate "0.01" \
--from garfield_wallet \
--gas=auto \
--fees 5000ulamb
```
#### Wallet 
```
# create a wallet
empowerd keys add $WALLET --keyring-backend os

# restore the wallet (after the command insert seed)
empowerd keys add $WALLET --recover --keyring-backend os

# export to metamask(view private key)
lambdavm keys unsafe-export-eth-key garfield_wallet

# export wallet from metamask
lambdavm keys unsafe-import-eth-key [account name] [private key]
```
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

#### Deleting
```
systemctl stop lambdavm && \
systemctl disable lambdavm && \
rm /etc/systemd/system/lambdavm.service && \
systemctl daemon-reload && \
cd $HOME && \
rm -rf .lambdavm lambdavm && \
rm -rf $(which lambdavm)
```
