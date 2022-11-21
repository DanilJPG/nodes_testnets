## Zeeka Network

[Discord](https://discord.gg/eT96kE6V) | [Website](https://zeeka.network/) | [Github](https://github.com/zeeka-network)
| --- | --- | ---
#### Active version incentive Tetnet run 21.11.22 !
![image](https://user-images.githubusercontent.com/57448493/192145552-6eed7477-d72a-4089-bf94-172f4deec8ff.png)

Zeeka (ℤ) is a cryptocurrency that aims to create a lightweight and scalable blockchain with extensive use of zero-knowledge proof technology.

Zeeka offers a new concept called Zero Contracts. Zero contracts are the equivalent of smart contracts in some major blockchain systems such as Ethereum. These contracts will be expressed as mathematical constraints instead of virtual machine byte codes, such as the Ethereum virtual machine.

Zeeka will incorporate concepts previously used as privacy layer or L2 solutions in other chains into the core of the new blockchain, aiming to create a more scalable network with better privacy.

At this point, it is possible to help the project in-house. There are 3 forms to fill out:

1. Community member form (for moderators):https://docs.google.com/forms/d/e/1FAIpQLSdz129RVXCPLIipF2evu5HDblo5iXdVBtk-3RO6XzKYCAVGlQ/viewform

2. Contributions form:https://docs.google.com/forms/d/e/1FAIpQLSewVt8hRnRcufFOLCm9E9tNSeQ9FgWBjmygyIScA6_c5H7NPg/viewform

3. Testnet submission form:https://docs.google.com/forms/d/e/1FAIpQLSdZVJmcL5X83zDUdRIJxuWiSi8hvmocEM7Ut8E0m97-cmdgcQ/alreadyresponded

Official website:https://zeeka.io/

Project GitHub:https://github.com/zeeka-network/bazuka

***
#### 1. Server Preparation
```
sudo apt update && sudo apt upgrade -y
sudo apt install wget jq git libssl-dev cmake -y
```
#### 2.Install Rust
```
. <(wget -qO- https://raw.githubusercontent.com/letsnode/Utils/main/installers/rust.sh)
```
#### 3.Clone a repository with a node
```
git clone https://github.com/zeeka-network/bazuka
```
#### 4.Перейти в папку bazuka,компиляция и установка
```
cd bazuka && cargo install --path
```

#### View software bazuka
```
/root/bazuka/target/release/bazuka -h
```
#### You can run with this command or in the next step through a service file
```
#Clear the state of the development chain:
target/release/bazuka purge-chain --dev
Start the development chain with detailed logging:

RUST_LOG=`RUST_LOG=info which bazuka` RUST_BACKTRACE=1 target/release/bazuka node --listen '0.0.0.0:8765' --external 'IP:8765' --network chaos --db /root/.bazuka-chaos --bootstrap '152.228.155.120:8765' --bootstrap '154.12.231.150:8765' --discord-handle 'nickname'  
```
#### 5.Initialize a node
```
bazuka init --network groth --bootstrap 65.108.193.133:8765 --mnemonic "YOUR OLD MNEMONIC PHRASE"
```
Expectation:
![image](https://user-images.githubusercontent.com/57448493/192145821-fe01f241-8795-48d9-b9aa-72b25db18b7e.png)

#### 6.Create a service file
```
sudo tee <<EOF >/dev/null /etc/systemd/system/zeeka.service
[Unit]
Description=Zeeka node
After=network.target

[Service]
User=$USER
ExecStart=`RUST_LOG=info which bazuka` node --listen 0.0.0.0:8765 --external [IP address]:8765 --network debug --db ~/.bazuka-debug --bootstrap 5.161.152.123:8765 --bootstrap 65.108.201.41:8765 --bootstrap 185.213.25.229:8765 --bootstrap 45.88.106.199:8765 --bootstrap 148.251.1.124:8765 --bootstrap 195.54.41.115:8765 --bootstrap 195.54.41.130:8765
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
Замените [IP address] на ip вашего сервера
```
#### 7.Run the service
```
sudo systemctl daemon-reload
sudo systemctl enable zeeka
sudo systemctl restart zeeka
```
Add a command to view the log of a node in the system as a variable
```
. <(wget -qO- https://raw.githubusercontent.com/AlexM-dev/Utils/main/commands/insert_variable.sh) -n zeeka_log -v "sudo journalctl -fn 100 -u zeeka" -a
```
#### View logs
```
bazuka node --listen 0.0.0.0:8765 --external [Your server IP address]:8765
zeeka_log
```
![image](https://user-images.githubusercontent.com/57448493/198363637-75ff9f4e-0a34-4b93-a5ce-14bdd053033b.png)


Copy the data to a safe place!!!

#### Delete a node 
```
sudo systemctl stop zeeka && \
sudo systemctl disable zeeka && \
rm /etc/systemd/system/zeeka.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf .bazuka && \
rm -rf $(which bazuka)
```

#### Update version 
```
rm ~/.bazuka.yaml
sudo systemctl stop zeeka 
cd bazuka
git pull origin master
cargo install --path
```

#### Useful Commands
```
Useful commands:
bazuka deposit Deposit funds to a Zero-Contract

bazuka help Prints this message or the help of the given subcommand(s)

bazuka init Initialize node/wallet

bazuka node Run node

bazuka rsend Send funds through a regular-transaction

bazuka status Get status of a node

bazuka withdraw Withdraw funds from a Zero-Contract

bazuka zsend Send funds through a zero-transaction
```
