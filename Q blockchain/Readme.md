## Q blockchain

*Q - это новый блокчейн, который сочетает в себе преимущества публичной, открытой и децентрализованной бухгалтерской книги с прозрачностью и предсказуемостью частных контрактов, подлежащих исполнению, что позволяет использовать его в самых разных вариантах использования, которые стремятся к децентрализации, но требуют масштабируемости и надежности.*

[Discord](https://discord.gg/py7MFZ9g) | [Website](https://q.org/) | [Explorer](https://stats.qtestnet.org/) | [Form](https://itn.qdev.li/)
--- | --- | --- | --- |

Steps | Comments
--- | --- |
[Server Preparation](https://github.com/DanilJPG/nodes_testnets/blob/main/Q%20blockchain/Readme.md#:~:text=Server%20preparation%20%2D%20%D0%9F%D0%BE%D0%B4%D0%B3%D0%BE%D1%82%D0%BE%D0%B2%D0%BA%D0%B0%20%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0) | Installing dependencies and utilities
[Setup Validator node](https://github.com/DanilJPG/nodes_testnets/blob/main/Q%20blockchain/Readme.md#:~:text=Setup%20Validator%20node%20%2D%20%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0%20%D1%83%D0%B7%D0%BB%D0%B0%20%D0%B2%D0%B0%D0%BB%D0%B8%D0%B4%D0%B0%D1%82%D0%BE%D1%80%D0%B0) | Working with the repository and generating block signing keys
[Configuration files setup](https://github.com/DanilJPG/nodes_testnets/blob/main/Q%20blockchain/Readme.md#:~:text=Configuration%20files%20setup%20%2D%20%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D0%BA%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D0%BE%D0%BD%D0%BD%D0%BE%D0%B3%D0%BE%20%D1%84%D0%B0%D0%B9%D0%BB%D0%B0) | Change the default settings to your own
[Lauch](https://github.com/DanilJPG/nodes_testnets/blob/main/Q%20blockchain/Readme.md#:~:text=4.-,Lauch,-docker%2Dcompose%20up) | lauch docker
[Appendix](https://github.com/DanilJPG/nodes_testnets/blob/main/Q%20blockchain/Readme.md#:~:text=Appendix%20%2D%20%D0%9F%D1%80%D0%B8%D0%BB%D0%BE%D0%B6%D0%B5%D0%BD%D0%B8%D0%B5) | 
[Possible Errors]() | 
[Upgarde](https://github.com/DanilJPG/nodes_testnets/blob/main/Q%20blockchain/Upgrade.md) | actual version


### 1. Server preparation - Подготовка сервера
```Shell
#update
sudo apt-get update && sudo apt-get upgrade -y

# install 'key'
sudo apt-get install curl wget git jq libpq-dev libssl-dev \
build-essential pkg-config openssl ocl-icd-opencl-dev \
libopencl-clang-dev libgomp1 -y

#install docker and docker-compose
. <(wget -qO- https://raw.githubusercontent.com/SecorD0/utils/main/installers/docker.sh)

#install rust
curl https://sh.rustup.rs -sSf | sh -s -- -y

#check version
source "$HOME/.cargo/env" && \
echo -e "\n$(cargo --version).\n"
```

### 2. Setup Validator node - Установка узла валидатора

#### 2.1 Working with repository and keys - Работа с репозиторием, бинарными файлами и ключами
```Shell
git clone https://gitlab.com/q-dev/testnet-public-tools
cd testnet-public-tools/testnet-validator
```
*This directory contains the docker-compose.yaml file for quick launching of the validator node with preconfigurations on rpc, blockchain explorer using .env (which can be created from .env.example).*

*Этот каталог содержит файл docker-compose.yaml для быстрого запуска узла валидатора с предварительными настройками на rpc, blockchain explorer с использованием .env (который может быть создан из .env.example).*

#### 2.2 Creating a catalog with keys and generating keys - Создание каталога с ключами и генерация ключей
*Создаем папку и файл где будет хранится пароль, который будет защищать наш файл с ключами*

*Create a folder and a file that will store the password, which will protect our file with the keys*

```Shell
mkdir keystore 
cd keystore
touch pwd
nano pwd.txt
# Save changes ctrl X , Y , enter
```
#### Generation file

```Shell
docker-compose run --rm --entrypoint "geth account new --datadir=/data --password=/data/keystore/pwd.txt" testnet-validator-node
```

**The generated file stores your address for Metamask - В сгенерированном файле хранится ваш адрес для Metamask**

*Таким образом, генерируется новый закрытый ключ, который хранится в каталоге /keystore, зашифрованном паролем из `pwd.txt` файл. В нашем примере `0xb3FF24F818b0ff6Cc50de951bcB8f86b52287DAc` (у вас будет другое значение) - это адрес, соответствующий недавно сгенерированному закрытому ключу.*

*This generates a new private key, which is stored in the /keystore directory, encrypted with the password from the `pwd.txt` file. In our example `0xb3FF24F818b0ff6Cc50de951bcB8f86b52287DAc` (you will have a different value) is the address corresponding to the newly generated private key.*
```Shell

Your new key was generated

Public address of the key:   0xb3FF24F818b0ff6Cc50de951bcB8f86b52287dac
Path of the secret key file: /data/keystore/UTC--2021-01-18T11-36-28.705754426Z--b3ff24f818b0ff6cc50de951bcb8f86b52287dac

- You can share your public address with anyone. Others need it to interact with you.
- You must NEVER share the secret key with anyone! The key controls access to your funds!
- You must BACKUP your key file! Without the key, it's impossible to access account funds!
- You must REMEMBER your password! Without the password, it's impossible to decrypt the key!
```

#### 2.3 [Faucet](https://faucet.qtestnet.org/)

### 3. Configuration files setup - Настройка конфигурационного файла 
```Shell
cp .env.example .env
nano .env
```

#### 3.1 Edit `.env`

**Replace `ADDRESS(without 0x)` obtained in step 2 and your `IP`  - Замените `ADDRESS(без 0х)` полученный в шаге 2 и ваш `IP` **
```Shell
# docker image for q client
QCLIENT_IMAGE=qblockchain/q-client:1.2.2

# your q address here (without leading 0x)
ADDRESS=<your address>

# your public IP address here
IP=<your IP>

# the port you want to use for p2p communication (default is 30313)
EXT_PORT=30313

# extra bootnode you want to use
BOOTNODE1_ADDR=enode://c610793186e4f719c1ace0983459c6ec7984d676e4a323681a1cbc8a67f506d1eccc4e164e53c2929019ed0e5cfc1bc800662d6fb47c36e978ab94c417031ac8@79.125.97.227:30304
BOOTNODE2_ADDR=enode://8eff01a7e5a66c5630cbd22149e069bbf8a8a22370cef61b232179e21ba8c7b74d40e8ee5aa62c54d145f7fc671b851e5ccbfe124fce75944cf1b06e29c55c80@79.125.97.227:30305
BOOTNODE3_ADDR=enode://7a8ade64b79961a7752daedc4104ca4b79f1a67a10ea5c9721e7115d820dbe7599fe9e03c9c315081ccf6a2afb0b6652ee4965e38f066fe5bf129abd6d26df58@79.125.97.227:30306

#Save changes ctrl X , Y , enter
```


#### 3.2 Edit `config.json`
```Shell
nano config.json
```
**Replace `ADDRESS(without 0x)` obtained in step 2.2 and password**
```Shell
{
 "address": "<your address>",
 "password": "supersecurepassword",
 "keystoreDirectory": "/data",
 "rpc": "https://rpc.qtestnet.org"
}

#Save changes ctrl X , Y , enter
```

#### 3.3 Put Stake in Validators Contract - Заложить долю в контракт валидаторов 
```Shell
Чтобы получать вознаграждения нужно сделать ставку на валидатора, перейдите `https://hq.qtestnet.org`. Включите режим `advanced mode` и сделайти ставку `Consensus Services` -> `Validator Staking`

As was mentioned previously, you need to put stake to validators contract in order to become a validator.

You can use the dApp "Your HQ" that can be found at `https://hq.qtestnet.org`. Ultimately, you need to Join Validator Ranking to receive rewards. The according functionality is located at `Consensus Services` -> `Validator Staking` in box "Manage Balance". If you can't see the menu item Consensus Services, you are not running the dApp UI in `advanced mode`. Go to Settings and activate it.
```

#### 3.4 Add your Validator to https://stats.qtestnet.org - Добавление вашего валидатора в общий список
```Shell
nano /testnet-validator/docker-compose.yaml
```

**Take a look at the example below.Copy and paste into `docker-compose.yaml`. Replace with `<your_name_validator>`. Save with ctrl X , Y , enter**

**Взгляните на пример ниже.Скопируйте и вставьте в `docker-compose.yaml`. Замените `<your_name_validator>`. Сохраните ctrl X , Y , enter**

```Shell

version: "3"

services:
  testnet-validator-node:
    image: $QCLIENT_IMAGE
    entrypoint: ["geth", "--ethstats=<your_name_validator>:qstats-testnet@stats.qtestnet.org", "--bootnodes=$BOOTNODE1_ADDR,$BOOTNODE2_ADDR,$BOOTNODE3_ADDR", "--datadir=/data", "--nat=extip:$IP", "--port=$EXT_PORT", "--unlock=$ADDRESS",  "--password=/data/keystore/pwd.txt", "--mine", "--miner.threads=1", "--syncmode=full", "--rpc.allow-unprotected-txs", "--testnet", "--verbosity=3", "--miner.gasprice=1"]
    volumes:
      - ./keystore:/data/keystore
      - ./additional:/data/additional
      - testnet-validator-node-data:/data
    ports:
      - $EXT_PORT:8545/tcp
      - $EXT_PORT:8545/udp
    restart: unless-stopped

volumes:
  testnet-validator-node-data:

```
### 4. Lauch
```Shell
docker-compose up -d
docker-compose logs -f --tail "100"
```
### Следите за обновлениями в канале - Watch the channel for updates

#### 5. Appendix - Приложение (This is what your work files should look like - примерно так должны выглядеть ваши рабочие файлы)
`.env`

![image](https://user-images.githubusercontent.com/57448493/208238274-1079d224-c610-4c19-a669-a3e590c0f8f0.png)

`docker-compose.yaml`

![image](https://user-images.githubusercontent.com/57448493/208238299-75df315f-ee58-4eef-9bf8-055e2fbdeb19.png)

`config.json`

![image](https://user-images.githubusercontent.com/57448493/208238346-aaae2b9b-31f5-4cb1-adee-ee37af4576af.png)

### 6. Oracle

`/omnibridge-oracle`	The core Q OmniBridge client
`/omnibridge-ui`	A graphical user interface to use the bridge on your local server
`/omnibridge-alm` The monitor component to track the status of bridge transactions on the blockchain

`/omnibridge-oracle` Основной клиент Q OmniBridge
`/omnibridge-ui` Графический пользовательский интерфейс для использования моста на вашем локальном сервере
`/omnibridge-alm` Компонент монитора для отслеживания статуса транзакций моста на блокчейне

#### 6.1 Omnibridge
```Shell
cd /root/testnet-public-tools/omnibridge-oracle/
cp .env.testnet .env
nano .env
```
Восстановите кошелек в метамаск с помощью файла который был сгенерирован в шаге : [generation UTC](https://github.com/DanilJPG/nodes_testnets/blob/main/Q%20blockchain/Readme.md#:~:text=X%20%2C%20Y%20%2C%20enter-,Generation%20file,-docker%2Dcompose%20run)

Restore the wallet to the metamask using the file that was generated in step : [generation UTC](https://github.com/DanilJPG/nodes_testnets/blob/main/Q%20blockchain/Readme.md#:~:text=X%20%2C%20Y%20%2C%20enter-,Generation%20file,-docker%2Dcompose%20run)

1) Скопируйте приватный ключ и вставьте в `ORACLE_VALIDATOR_ADDRESS_PRIVATE_KEY` 
2) Замените `ORACLE_VALIDATOR_ADDRESS` на ваш адрес кошелька
3) `COMMON_FOREIGN_RPC_URL` заменить на `https://rpc.ankr.com/eth_rinkeby`


1) Copy the private key and paste it into `ORACLE_VALIDATOR_ADDRESS_PRIVATE_KEY`. 
2) Replace `ORACLE_VALIDATOR_ADDRESS` with your wallet address
3) `COMMON_FOREIGN_RPC_URL` here we write `https://rpc.ankr.com/eth_rinkeby`
![image](https://user-images.githubusercontent.com/57448493/208241342-e404c324-7796-4fec-bd88-481184d66bd1.png)

#Save changes ctrl X , Y , enter
```Shell
docker-compose up -d
screen -S oracle
docker-compose logs -f --tail "100"
```
#### 6.2 Omnibridge-ui
```Shell
cd ../omnibridge-ui
cp .env.testnet .env
nano .env
```
`REACT_APP_FOREIGN_RPC_URL` replace `https://rpc.ankr.com/eth_rinkeby`

```Shell
#Save changes ctrl X , Y , enter
docker-compose up -d
```

#### 6.3 Omnibridge-alm
```Shell
cd
cd testnet-public-tools/omnibridge-alm/
cp .env.testnet .env
nano .env
```
`COMMON_FOREIGN_RPC_URL` replace `https://rpc.ankr.com/eth_rinkeby`

```Shell
#Save changes ctrl X , Y , enter
docker-compose up -d
```
