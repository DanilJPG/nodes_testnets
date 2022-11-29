#### State Sync
```Bash
sudo systemctl stop empowerd

cp $HOME/.empowerchain/data/priv_validator_state.json $HOME/.empowerchain/priv_validator_state.json.backup
empowerd tendermint unsafe-reset-all --home $HOME/.empowerchain --keep-addr-book

SNAP_RPC=http://5.161.99.245:26657/

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers=""18546cc8e240275acc7133e3ed01e9b58d0d134a@5.161.99.245:26656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.empowerchain/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.empowerchain/config/config.toml

mv $HOME/.empowerchain/priv_validator_state.json.backup $HOME/.empowerchain/data/priv_validator_state.json

sudo systemctl restart empowerd
sudo journalctl -u empowerd -f --no-hostname -o cat
```
