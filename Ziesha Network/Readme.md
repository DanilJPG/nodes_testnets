## Zeeka Network

[Discord](https://discord.gg/zieshanetwork) | [Website](https://zeeka.io/) | [Github](https://github.com/zeeka-network)
| --- | --- | ---
#### Active version incentive Tetnet run 21.11.22 !

***
#### 1. Server Preparation
```Bash
sudo apt update && sudo apt upgrade -y
sudo apt install wget jq git libssl-dev cmake -y
```
#### 2.Install Rust
```Bash
. <(wget -qO- https://raw.githubusercontent.com/letsnode/Utils/main/installers/rust.sh)
```
#### 3.Clone a repository with a node
```Bash
git clone https://github.com/ziesha-network/bazuka
```
#### 4.Перейти в папку bazuka,компиляция и установка
```Bash
cd bazuka && cargo install --path /root/bazuka/
```

#### View software bazuka
```Bash
/root/bazuka/target/release/bazuka -h
```

#### 5.Initialize a node
```Bash
bazuka init --listen 0.0.0.0:8765 --db ~/.bazuka --bootstrap 31.210.53.186:8765 --bootstrap 89.179.68.98:8765 --mnemonic "<mnemonic>"
```

#### 6.Create a service file
```Bash
sudo tee <<EOF >/dev/null /etc/systemd/system/ziesha.service
[Unit]
Description=Zeeka node
After=network.target

[Service]
User=$USER
ExecStart=`RUST_LOG=info which bazuka` node start --discord-handle "$ZEEKADISCORD"
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
#### 7.Run the service
```Bash
sudo systemctl daemon-reload
sudo systemctl enable ziesha
sudo systemctl restart ziesha
```

#### 8.Check
```
sudo journalctl -f -u ziesha
```

#### Delete a node 
```Bash
systemctl stop zeeka
systemctl disable zeeka
rm -rf /root/bazuka
rm ~/.bazuka.yaml
```

#### Update version 
```Bash
rm ~/.bazuka.yaml
rm -rf ~/.bazuka_wallet.yaml
sudo systemctl stop zeeka 
cd bazuka
git pull origin master
cargo install --path
```

#### Useful Commands


`bazuka deposit` Deposit funds to a Zero-Contract

`bazuka help` Prints this message or the help of the given subcommand(s)

`bazuka init` Initialize node/wallet

`bazuka node` Run node

`bazuka rsend` Send funds through a regular-transaction

`bazuka node status` Get status of a node

`bazuka withdraw` Withdraw funds from a Zero-Contract

`bazuka zsend` Send funds through a zero-transaction

