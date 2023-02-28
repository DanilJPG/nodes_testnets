## Nibiru Chain

[Discord](https://discord.gg/4sCtbbfc) | [Website](https://nibiru.fi/) | [Faucet](https://discord.com/channels/947911971515293759/984840062871175219)
| --- | --- | ---

Steps | Comments
--- | --- |
[Server Preparation](https://github.com/DanilJPG/nodes_testnets/tree/main/Nibiru%20Chain#:~:text=1.%D0%9F%D0%BE%D0%B4%D0%B3%D0%BE%D1%82%D0%BE%D0%B2%D0%BA%D0%B0%20%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0%20%2D%20Server%20Preparation) | Check the version, if necessary update to the appropriate height blocks,server setup, Go language is necessary to work with a binary file and unpack it
[Working with a binary file and setting up](https://github.com/DanilJPG/nodes_testnets/blob/main/Nibiru%20Chain/Readme.md#:~:text=%5C%0Ago%20version-,2.%D0%A0%D0%B0%D0%B1%D0%BE%D1%82%D0%B0%20%D1%81%20%D0%B1%D0%B8%D0%BD%D0%B0%D1%80%D0%BD%D1%8B%D0%BC%20%D1%84%D0%B0%D0%B9%D0%BB%D0%BE%D0%BC%20%D0%B8%20%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%2D%20Working%20with%20a%20binary%20file%20and%20setting%20up,-Cloning%20a%20repository) | Cloning the GitHub repository of a project,To generate configuration files,The genesis stores the state of the chain,Making changes to config.toml,Creating a service file
[Creating a validator and generating a wallet](https://github.com/DanilJPG/nodes_testnets/tree/main/Nibiru%20Chain#:~:text=3.%D0%A1%D0%BE%D0%B7%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5%20%D0%B2%D0%B0%D0%BB%D0%B8%D0%B4%D0%B0%D1%82%D0%BE%D1%80%D0%B0%20%D0%B8%20%D0%B3%D0%B5%D0%BD%D0%B5%D1%80%D0%B0%D1%86%D0%B8%D1%8F%20%D0%BA%D0%BE%D1%88%D0%B5%D0%BB%D1%8C%D0%BA%D0%B0%20%2D%20Creating%20a%20validator%20and%20generating%20a%20wallet) | Creating and restoring a wallet,Creating your node operator
[Useful commands](https://github.com/DanilJPG/nodes_testnets/blob/main/Nibiru%20Chain/Useful%20commands.md) | Here are commands for the validator, for node management and for the wallet
[Possible Errors](https://github.com/DanilJPG/nodes_testnets/blob/main/Nibiru%20Chain/Possible%20errors.md) | Here are possible errors related to the node and the validator
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
git clone https://github.com/NibiruChain/nibiru && cd nibiru
make install
mv /root/go/bin/nibid /usr/bin/
chmod +x /usr/bin/nibid
nibid version
```

#### Initializing
```Shell
nibid init <name_moniker> --chain-id nibiru-itn-1 --home $HOME/.nibid \
nibid config chain-id nibiru-testnet-2
```


#### Download genesis and address book
```Shell
wget -O https://raw.githubusercontent.com/Pa1amar/testnets/main/nibiru/nibiru-itn-1/genesis.json
```


#### Correct the configuration file
```Shell
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025unibi\"/;" ~/.nibid/config/app.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.nibid/config/config.toml

sed -i 's|seeds =.*|seeds = "'$(curl -s https://networks.itn.nibiru.fi/$NETWORK/seeds)'"|g' $HOME/.nibid/config/config.toml
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.nibid/config/config.toml

PEERS="ac163da500a9a1654f5bf74179e273e2fb212a75@65.108.238.147:27656,abab2c6f45fa865dc61b2757e21c5d2244e5bacb@213.202.218.55:26656,fe17db7c9a5f8478a2d6a39dbf77c4dc2d6d7232@5.75.189.135:26656,baff3597ebd19ce273b7c0b56e2d50a8964d1423@65.109.90.166:26656,ca6213c897bd8400d8d01b947a541db85ebb2d96@51.89.199.49:36656,63256b5937ac438e3b21b570a07ace6ddc3bd0c6@194.163.182.122:39656,c1b40d056e4260a9fa9d1142af1adbeec5039599@142.132.202.50:46656,ea44a000ee4df9d722a90fdf41b3990e738bdda0@65.109.235.95:26656,7e75b2249d088a4dfc3b33f386c316cb47366d2b@195.3.221.48:11656,e08089921baf39382920a4028db9e5eebd82f3d7@142.132.199.236:21656,2484b3b0912815869317e1da43a409b9ffd6653e@154.12.244.128:26656,9946c87d01312752d26fe0ceef4f4e24707f8144@65.109.88.178:27656,d327bb6b997a32aaa7dae5673e9a9cbad487ad09@104.156.250.70:26656,4f1af4f62f76c095d844384a3dfa1ad76ad5c078@65.108.206.118:60656,6052d09554a442f22f71c33dbc5f25bee538e087@65.109.82.249:28656,c4124e6623529b31b8c535be1ea8835aa7ff51b0@51.79.77.103:28656,2dce4b0844754b467ae40c9d6360ac51836fadca@135.181.221.186:29656,c8907a13b012e7a937cfe7d624b0fbe7ef3508b2@194.163.160.155:26656,0ebf64601e93d0e5304da8b7d3deb96d7d7cbcf8@176.120.177.123:26656,30e14f66fc44a55a51f36693afd754283c668953@65.108.200.60:11656,fa5c730d842aff05c3761d9c1b06107340ac7651@65.108.232.238:11656,f01ad3a75b255226499df9183ac2ebc0a40a9e05@46.4.53.207:33656,81a8383eefae628ae4bc400d52d49adfb11cb76a@65.108.108.52:11656,1c548375968f0abfac3733cae9f592468c988bf9@46.4.53.209:33656,f4a6bcbd4af5cfd82ee3a40c54800176e33e9477@31.220.79.15:26656,b03d1ce3e97984a8b8a63a7a6ec6c5d196d81436@46.4.53.208:33656,e74f1204d65d0264547e2c2d917c23c39fcff774@95.217.107.96:36656,b316ff6b5a0715732fa02f990db94aef39e758b3@148.251.88.145:10156,79e2bfc202e39ba2a168becc4c75cb6a56803e38@135.181.57.104:11656,22d5b4919850ad71ad0a1bf7979c7dba53960689@192.9.134.157:27656,2686c58fc276fff2956bf1b10736244737f84c9b@178.208.86.44:26656,a3a344c1732c507f40931778225f919004392e94@52.204.188.236:26656,84d888be939b738d343db0613d4cc50a33f36beb@158.101.208.86:26656,21ad250f917fafcd9bca8ea223558dffc6bd75c4@38.242.205.18:26656,1848442cbab24bc7123ad2dec2464661b5bc92c1@94.190.90.38:28656,e9b25db508b31cb9d48b1f0b67147faf8c2b7b0b@65.108.199.206:27656"

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
systemctl enable nibid
systemctl restart nibid
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
--chain-id nibiru-itn-1 \
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

### 4. Удаление - Delete
#### Deleting
```Shell
systemctl stop nibid && \
systemctl disable nibid && \
rm /etc/systemd/system/nibid.service && \
systemctl daemon-reload && \
cd $HOME && \
rm -rf .nibid nibiru && \
rm -rf $(which nibid)
```
