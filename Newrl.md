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
