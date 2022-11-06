## Установка полного узла
Website: https://celestia.org/

Docs: https://docs.celestia.org/

Explorers: https://celestia.explorers.guru/

Github: https://github.com/celestiaorg

#### Обновление и установка "ключей" и утилит
```
sudo apt update && sudo apt upgrade -y \
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu -y
```
#### Установка Go
```
cd $HOME
ver="1.19.1"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile

go version
```
