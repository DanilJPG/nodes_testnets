#### mainnet
```
sudo ufw status
```
```
sudo ufw allow 22
sudo ufw allow 22/tcp
sudo ufw allow 21
sudo ufw allow 21/tcp
sudo ufw allow 8456
sudo ufw allow 8456/tcp
```
```
sudo ufw enable
```
#### Обновление и подготовка сервера
```
sudo apt-get update && sudo apt-get upgrade \
git clone https://github.com/git/git \
```
#### Python install
```
sudo apt update && sudo apt install python3.10-venv
sudo apt update
sudo apt install python3-venv python3-pip
```
#### Установка скрипта бинарника
```
cd newrl
# остановка работы скрипта из тестнета
screen -X -S newrl kill
git clone https://github.com/newrlfoundation/newrl.git
cd newrl
scripts/install.sh mainnet
```
#### Запуск 
```
apt install screen
screen -S newrl
scripts/start.sh mainnet
```
#### Backup
```
cd newrl
python3 scripts/show_wallet.py
```

#### Добавление доверия для создания блоков
```

```
#### Update testnet 20.10
```
cd newrl

screen -X -S newrl quit

scripts/install.sh testnet

screen -S newrl

scripts/start.sh testnet
```
#### Updating and installing dependencies
```
apt-get install update && apt-get upgrade -y && \
sudo apt install -y build-essential libssl-dev libffi-dev git curl screen pacman
```
```
Installing Pyth
sudo apt update
sudo apt install python3-venv python3-pip
sudo pacman -S python-pip

curl -sSL https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python3 get-pip.py
sudo apt install python3.9-venv
sudo mkdir newrl-venv
cd newrl-venv
python3.9 -m venv newrl-venv
```
#### Updating variables 
```
source newrl-venv/bin/activate
```

#### clone repo
```
cd
git clone https://github.com/newrlfoundation/newrl.git
cd newrl
scripts/install.sh testnet
```

#### wallet
```
python3 scripts/show_wallet.py
```
### Save the output text with the address and keys

#### 1.Follow this link
 https://wallet.newrl.net/login?next=/wallet

#### 2.Create a password, then go to 'wallet' and import the wallet, paste everything you saved after python3 scripts/show_wallet.py with brackets {}

#### 3.Go to faucet and ask for tokens to our address

#### 4. Stake your validator

#### chek 
Follow this link http://archive1-testnet1.newrl.net:8421/sc-state?table_name=stake_ledger&contract_address=ct1111111111111111111111111111111111111115&unique_column=wallet_address&unique_value=<wallet_adr>
