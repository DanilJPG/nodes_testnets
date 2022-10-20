```
git clone https://github.com/okp4/okp4d.git
cd okp4d
make install
```
```
okp4d version
```
```
okp4d init garfield --chain-id okp4-testnet-1
```

```
This will generate, in the $HOME/.okp4d folder, the following files:

config/app.toml: Application-related configuration file.
config/client.toml: Client-oriented configuration file (not used when running a node).
config/config.toml: Tendermint-related configuration file.
config/genesis.json: The network's genesis file.
config/node_key.json: Private key to use for node authentication in the p2p protocol.
config/priv_validator_key: Private key to use as a validator in the consensus protocol.
data: The node's database.
```
