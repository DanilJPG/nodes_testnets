## Manta Network
| [Discord](https://discord.gg/mantanetwork) | [Docs](https://docs.manta.network/docs/guides/TrustedSetup)
| --- | ---

![image](https://user-images.githubusercontent.com/57448493/205853439-05ca86de-38d4-48db-815f-c5702ce72f63.png)


#### Requirements
```Bash
Official: None. But I would bet on:
2сpu — 4vCPU
4–8 GB RAM
```
#### Подготовка сервера - Server Preparation
```Bash
sudo apt update && sudo apt upgrade -y
sudo apt install make clang pkg-config libssl-dev libclang-dev build-essential git curl ntp jq llvm tmux htop screen unzip cmake -y
curl https://sh.rustup.rs/ -sSf | sh -s -- -y
source $HOME/.cargo/env
```
#### Установка клиента - Installing client 
```Bash
sudo apt update

sudo apt install pkg-config build-essential libssl-dev curl jq

curl https://sh.rustup.rs/ -sSf | sh -s -- -y

source $HOME/.cargo/env

git clone https://github.com/Manta-Network/manta-rs.git

cd manta-rs

cargo run --release --package manta-trusted-setup --all-features --bin groth16_phase2_client register
```
After installation, the terminal will prompt you to enter your Twitter and e-mail, and immediately generate a key, which must be saved somewhere - 
После уставновки терминал предложит вам вбить ваш Twitter и e-mail, и сразуже сгенерирует ключи, который обязательно нужно куда-то сохранить.
![image](https://user-images.githubusercontent.com/57448493/205856767-6f9ed2a0-50c0-40c7-8bef-b8174ce058bb.png)

#### Registration
```Bash
cd ~

mv /root/manta-rs/target/release/groth16_phase2_client /usr/bin/manta-trusted-setup

chmod +x /usr/bin/manta-trusted-setup

screen -S manta

manta-trusted-setup contribute #вводим seed - enter seed
Пока что регистрация закрыта, ждемс когда откроют
```
![image](https://user-images.githubusercontent.com/57448493/205856806-61ce3a8a-2fd4-4837-aa02-f000f7b0c1f1.png)
![image](https://user-images.githubusercontent.com/57448493/205856870-02a725c2-2102-4abf-a38b-402547da148a.png)
