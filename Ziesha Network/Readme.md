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
cd bazuka && cargo install --path
```

#### View software bazuka
```Bash
/root/bazuka/target/release/bazuka -h
```

#### 5.Initialize a node
```Bash
bazuka init --listen 0.0.0.0:8765 --db ~/.bazuka --network pelmeni --external <IP>:8765 --bootstrap 23.34.12.45:8765 --bootstrap 34.56.78.23:8765 --mnemonic "<mnemonic>"
```

#### 6.Create a service file
```Bash
sudo tee <<EOF >/dev/null /etc/systemd/system/zeeka.service
[Unit]
Description=Zeeka node
After=network.target

[Service]
User=$USER
ExecStart=`RUST_LOG=info which bazuka` node start --discord-handle <discord_handle> 
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
sudo systemctl enable zeeka
sudo systemctl restart zeeka
```
Add a command to view the log of a node in the system as a variable
```
. <(wget -qO- https://raw.githubusercontent.com/AlexM-dev/Utils/main/commands/insert_variable.sh) -n zeeka_log -v "sudo journalctl -fn 100 -u zeeka" -a
```
#### View logs
```Bash
zeeka_log
```
![Screenshot_2](https://user-images.githubusercontent.com/57448493/203043786-13920c84-4b91-44f5-829e-d87fdac7d60f.png)

Copy the data to a safe place!!!

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

