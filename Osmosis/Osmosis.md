# обновляем репозитории
```apt update && sudo apt upgrade -y```

# устанавливаем необходимые утилиты
```apt install curl build-essential git wget jq make gcc tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y```

# проверяем работу жестких дисков
```curl -sL yabs.sh | bash -s — -ig```

# проверяем работу интернета
```
curl -sL yabs.sh | bash -s — -fg

ver="1.21.3" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

# Binary
```
git clone https://github.com/osmosis-labs/osmosis osmosis
cd osmosis
git checkout v23.0.0-rc1
make install
```

# init
```
osmosisd init YOUR_MONIKER --chain-id osmo-test-5```

# Genesis and Addrbok
```
wget -O genesis.json https://snapshots.polkachu.com/testnet-genesis/osmosis/genesis.json --inet4-only
mv genesis.json ~/.osmosisd/config

wget -O addrbook.json https://snapshots.polkachu.com/testnet-addrbook/osmosis/addrbook.json --inet4-only
mv addrbook.json ~/.osmosisd/config
```

# Peers

```
PEERS=67da6267ea12c9d9701a7a70cb9904f23fe610b0@95.217.200.98:10656,ba594c36b5a69bcb7553c18298af149363294886@94.72.101.254:29656,e5dc7e6e229c6d38baae3b8ac5edf96735b4f1c1@65.21.196.181:26656,9180bf213a35fc0b9f0b3e9b29f9f5c23a697450@220.130.223.158:29856,9ee720447a1b7f068d0158dae6592ef71d52d497@212.126.35.132:26656
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.osmosisd/config/config.toml
```

