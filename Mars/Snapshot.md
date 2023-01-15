## Snapshot 
```
sudo systemctl stop marsd
cp $HOME/.mars/data/priv_validator_state.json $HOME/.mars/priv_validator_state.json.backup
rm -rf $HOME/.mars/data
```

```
curl -L https://snapshots.kjnodes.com/mars-testnet/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.mars
mv $HOME/.mars/priv_validator_state.json.backup $HOME/.mars/data/priv_validator_state.json
```
```
sudo systemctl start marsd && sudo journalctl -u marsd -f --no-hostname -o cat
```
