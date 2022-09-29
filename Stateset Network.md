# Подача Gentx

#### Обновляемся 
```
sudo apt update && sudo apt upgrade -y
```
#### Установка зависимости
```
sudo apt install make clang pkg-config libssl-dev build-essential git jq ncdu bsdmainutils -y < "/dev/null"
```

#### Установка GO
```
cd $HOME
wget -O go1.18.2.linux-amd64.tar.gz https://go.dev/dl/go1.18.2.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.18.2.linux-amd64.tar.gz && rm go1.18.2.linux-amd64.tar.gz
echo 'export GOROOT=/usr/local/go' >> $HOME/.bashrc
echo 'export GOPATH=$HOME/go' >> $HOME/.bashrc
echo 'export GO111MODULE=on' >> $HOME/.bashrc
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bashrc && . $HOME/.bashrc
go version
```
#### Установка Starport 
```
curl https://get.starport.network/starport! | bash
```

#### Клонируем репозиторий 
```
git clone https://github.com/stateset/core
cd core
```

#### Билдим конфиг
```
starport chain build
```
#### Инициализируем, ` moniker-name` вставляем имя своего моникера
```
statesetd init moniker-name --chain-id=stateset-1-testnet
```
#### Создаем кошелек, вместо `key-name ` вставляем имя своего кошелька
```
statesetd keys add key-name
```
#### Добавляем аккаунт в генезис
```
statesetd add-genesis-account $(statesetd keys show key-name -a) 10000000000ustate
```
#### Наполняем аккаунт
```
statesetd gentx CUZDANisimi 9000000000ustate \
  --commission-max-change-rate "0.05" \
  --commission-max-rate "0.20" \
  --commission-rate "0.01" \
  --chain-id=stateset-1-testnet \
  --moniker "`moniker-name`" \
  --identity "`keybasedeki no`" \
  --website "`site twitter felen`" \
  --details "`kafana gore bisiler yaz`" \
  --security-contact="`mail adresi yaz`"
```
