## Nibiru Chain

[Discord](https://discord.gg/4sCtbbfc) | [Website](https://nibiru.fi/) | [Faucet](https://discord.com/channels/947911971515293759/984840062871175219)
| --- | --- | ---

Steps | Comments
--- | --- |
[Server Preparation](https://github.com/DanilJPG/nodes_testnets/blob/main/Nibiru%20Chain/Readme.md#:~:text=1.%D0%9F%D0%BE%D0%B4%D0%B3%D0%BE%D1%82%D0%BE%D0%B2%D0%BA%D0%B0%20%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0%20%2D%20Server%20Preparation) | Check the version, if necessary update to the appropriate height blocks,server setup, Go language is necessary to work with a binary file and unpack it
[Working with a binary file and setting up](https://github.com/DanilJPG/mainnet_guides/blob/main/BeeZee/Readme.md#:~:text=Cloning%20a%20repository) | Cloning the GitHub repository of a project,To generate configuration files,The genesis stores the state of the chain,Making changes to config.toml,Creating a service file
[Creating a validator and generating a wallet](https://github.com/DanilJPG/mainnet_guides/blob/main/BeeZee/Readme.md#:~:text=t%20%5C%0A%2D%2Dfees%205000ubze-,Wallet,-%23%20%D1%81%D0%BE%D0%B7%D0%B4%D0%B0%D1%82%D1%8C%20%D0%BA%D0%BE%D1%88%D0%B5%D0%BB%D0%B5%D0%BA%0Abzed) | Creating and restoring a wallet,Creating your node operator
[Useful commands](https://github.com/DanilJPG/mainnet_guides/blob/main/BeeZee/Useful%20command.md) | Here are commands for the validator, for node management and for the wallet
[State Sunc](https://github.com/DanilJPG/mainnet_guides/blob/main/BeeZee/State%20Sync.md) | Chain synchronization

### 1.Подготовка сервера - Server Preparation 
#### Обновление и установка зависимостей - Upgrade and install dependencies
```Shell
apt update && apt upgrade -y \

apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```

#### Go
```Shell
ver="1.19.1" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

### 2.Работа с бинарным файлом и настройка - Working with a binary file and setting up
#### Cloning a repository 
```Shell
git clone https://github.com/NibiruChain/nibiru
cd nibiru
git checkout v0.15.0
make install
mv /root/go/bin/nibid /usr/bin/
chmod +x /usr/bin/nibid
nibid version
```

#### Initializing
```Shell
nibid init <moniker-name> --chain-id=nibiru-testnet-1 --home $HOME/.nibid \
nibid config chain-id nibiru-testnet-1
```


#### Download genesis and address book
```Shell
curl -s https://rpc.testnet-1.nibiru.fi/genesis | jq -r .result.genesis > genesis.json
mv genesis.json $HOME/.nibid/config/genesis.json

wget -O $HOME/.nibid/config/addrbook.json "http://65.108.6.45:8000/nibiru/addrbook.json"
```


#### Correct the configuration file
```Shell
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
```Shell
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
```Shell
systemctl daemon-reload
systemctl start nibid
systemctl enable nibid
sudo journalctl -u nibid -f --no-hostname -o cat
```

### 3.Создание валидатора и генерация кошелька - Creating a validator and generating a wallet
#### Wallet 
```Shell
# создать кошелек
nibid keys add <name_wallet> --keyring-backend os

# восстановить кошелек (после команды вставить seed)
nibid keys add <name_wallet> --recover --keyring-backend os
```


#### Creating a validator
```Shell
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

#### Delete
```Shell
systemctl stop nibid && \
systemctl disable nibid && \
rm /etc/systemd/system/nibid.service && \
systemctl daemon-reload && \
cd $HOME && \
rm -rf .nibid nibiru && \
rm -rf $(which nibid)
```

#### If there is a problem connecting peers or genesis, try resetting the network and start with the genesis download step
```Shell
systemctl stop nibid
rm $HOME/.nibid/config/addrbook.json
nibid tendermint unsafe-reset-all --home $HOME/.nibid --keep-addr-book
```

#### State Sync
```Shell
sudo systemctl stop nibid

cp $HOME/.nibid/data/priv_validator_state.json $HOME/.nibid/priv_validator_state.json.backup
nibid tendermint unsafe-reset-all --home $HOME/.nibid --keep-addr-book

SNAP_RPC="https://5.161.99.245:36657"

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
