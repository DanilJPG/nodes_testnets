#### Обновляемся и устанавливаем зависимости
sudo apt update && sudo apt upgrade -y && \
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y

#### Установка Go
```
ver="1.19" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

#### Клонируем репозиторий 
```
git clone https://github.com/defund-labs/defund
cd defund
git checkout v0.1.0-alpha
make install
```

#### Инициализируем 
```
defundd init <moniker> --chain-id defund-private-2 \
defundd config chain-id defund-private-2
```

#### Скачиваем Genesis и Addrbook
```

```
#### Правим конфиг
```

```

#### Создаем сервисный файл
```

```

#### Wallet
```

```
