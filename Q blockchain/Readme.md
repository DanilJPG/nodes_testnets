## Q blockchain

*Q - это новый блокчейн, который сочетает в себе преимущества публичной, открытой и децентрализованной бухгалтерской книги с прозрачностью и предсказуемостью частных контрактов, подлежащих исполнению, что позволяет использовать его в самых разных вариантах использования, которые стремятся к децентрализации, но требуют масштабируемости и надежности.*

[Discord](https://discord.gg/py7MFZ9g) | [Website](https://q.org/) | [Explorer](https://stats.qtestnet.org/) | [Form](https://itn.qdev.li/)
--- | --- | --- | --- |

Steps | Comments
--- | --- |
[Server Preparation]() | Check the version, if necessary update to the appropriate height blocks,server setup, Go language is necessary to work with a binary file and unpack it
[Working with a binary file and setting up]() | Cloning the GitHub repository of a project,To generate configuration files,The genesis stores the state of the chain,Making changes to config.toml,Creating a service file
[Creating a validator and generating a wallet]() | Creating and restoring a wallet,Creating your node operator
[Useful commands]() | Here are commands for the validator, for node management and for the wallet
[Possible Errors]() | Here are possible errors related to the node and the validator


### Server preparation - Подготовка сервера
```

```

### Setup Validator node - Установка узла валидатора
#### Working with repository and keys - Работа с репозиторием, бинарными файлами и ключами
```
git clone https://gitlab.com/q-dev/testnet-public-tools
cd testnet-public-tools/testnet-validator
```
This directory contains the docker-compose.yaml file for quick launching of the validator node with preconfigurations on rpc, blockchain explorer using .env (which can be created from .env.example).

Этот каталог содержит файл docker-compose.yaml для быстрого запуска узла валидатора с предварительными настройками на rpc, blockchain explorer с использованием .env (который может быть создан из .env.example).

#### Creating a catalog with keys and generating keys - Создание каталога с ключами и генерация ключей
*Создаем папку и файл где будет хранится пароль, который будет защищать наш файл с ключами*
Create a folder and a file that will store the password, which will protect our file with the keys
```
mkdir keystore 
cd keystore
touch pwd
nano pwd.txt
# Write the password into the file and save it, exit ctrl X + Y + enter
```
```
docker-compose run --rm --entrypoint "geth account new --datadir=/data --password=/data/keystore/pwd.txt" testnet-validator-node
```
*Таким образом, генерируется новый закрытый ключ, который хранится в каталоге /keystore, зашифрованном паролем из pwd.txt файл. В нашем примере 0xb3FF24F818b0ff6Cc50de951bcB8f86b52287DAc (у вас будет другое значение) - это адрес, соответствующий недавно сгенерированному закрытому ключу.*

*This generates a new private key, which is stored in the /keystore directory, encrypted with the password from the pwd.txt file. In our example 0xb3FF24F818b0ff6Cc50de951bcB8f86b52287DAc (you will have a different value) is the address corresponding to the newly generated private key.*
```
Your new key was generated

Public address of the key:   0xb3FF24F818b0ff6Cc50de951bcB8f86b52287dac
Path of the secret key file: /data/keystore/UTC--2021-01-18T11-36-28.705754426Z--b3ff24f818b0ff6cc50de951bcb8f86b52287dac

- You can share your public address with anyone. Others need it to interact with you.
- You must NEVER share the secret key with anyone! The key controls access to your funds!
- You must BACKUP your key file! Without the key, it's impossible to access account funds!
- You must REMEMBER your password! Without the password, it's impossible to decrypt the key!
```
#### [Faucet](https://faucet.qtestnet.org/)

### Configuration file setup - Настройка конфигурационного файла 
```

```
