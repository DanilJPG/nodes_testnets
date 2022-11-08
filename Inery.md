Discord: https://discord.gg/GVBGr8uD

Website: https://inery.io/
Требования к оборудованию
```
CPU	Intel Core i3 or i5
RAM	4 GB DDR4 RAM
Storage	500 GB HDD
Connection	100 Mbit/s port
```
Рекомендуемые требования к компонентам
```
CPU	Intel Core i7-8700 Hexa-Core
RAM	64 GB DDR4 RAM
Storage	2 x 1 TB NVMe SSD
Connection	1 Gbit/s port
```

#### Подготовка(установка зависимостей,firewalld,открываем порты)
```
sudo apt-get update && sudo apt install git && sudo apt install screen

sudo apt-get install firewalld 
sudo systemctl start firewalld 
sudo systemctl enable firewalld 
sudo firewall-cmd --set-default-zone=public 
sudo firewall-cmd --zone=public --add-port=22/tcp --permanent 
sudo firewall-cmd --zone=public --add-port=8888/tcp --permanent 
sudo firewall-cmd --zone=public --add-port=9010/tcp --permanent 
sudo firewall-cmd --reload 
sudo systemctl restart firewalld

sudo apt-get install -y make bzip2 automake libbz2-dev libssl-dev doxygen graphviz libgmp3-dev \
autotools-dev libicu-dev python2.7 python2.7-dev python3 python3-dev \
autoconf libtool curl zlib1g-dev sudo ruby libusb-1.0-0-dev \
libcurl4-gnutls-dev pkg-config patch llvm-7-dev clang-7 vim-common jq libncurses5
```
### Task 1
#### Загрузка пакета Inery Node
```
git clone  https://github.com/inery-blockchain/inery-node
```
После завершения загрузки перейдите в каталог inery.node
```
cd inery-node
```
Внутри inery-node есть каталоги inery и inery.setup Каталог inery содержит все двоичные файлы, чтобы протокол blockchain работал, путь к этим двоичным файлам должен быть экспортирован в среду операционной системы
```
ls    
inery inery.setup
```
Перейдите в каталог inery.setup
```
cd inery.setup
```
Внутри inery.setup есть ine.py и каталог инструментов

Дать ine.py разрешение скрипта на выполнение с помощью команды "chmod":
```
chmod +x ine.py
```
Чтобы экспортировать путь к локальной среде операционной системы для двоичных файлов inery, внутри inery.setup запустите ine.py сценарий с параметрами --export
```
./ine.py --export
```
Обновление среды
```
cd; source .bashrc; cd -
```
#### Измените конфигурацию конфиг файла
Вставьте свои данные
```
sudo nano tools/config.json

"MASTER_ACCOUNT": {     
"NAME": "AccountName",     
"PUBLIC_KEY": "PublicKey",     
"PRIVATE_KEY": "PrivateKey",     
"PEER_ADDRESS": "IP:9010",     
"HTTP_ADDRESS": "0.0.0.0:8888",     
"HOST_ADDRESS": "0.0.0.0:9010" }
```
#### Запуск узла
```
screen -S inery
./ine.py --master
```
#### Привязка пошелька и аккаунта
```
# Создание кошелька
cd;  cline wallet create --file defaultWallet.txt

# Разблокировка кошелька
cline wallet unlock --password YOUR_WALLET_PASSWORD
 
cline wallet import --private-key MASTER_PRIVATE_KEY
  
cline system regproducer ACCOUNT_NAME ACCOUNT_PUBLIC_KEY 0.0.0.0:9010
  
cline system makeprod approve ACCOUNT_NAME ACCOUNT_NAME
```

### Task 2
Разблокировка кошелька
```
cline wallet unlock -n <name_wallet>

password:...
```
Мы возьмем контракт токена из уже существующего, скопировав код контракта inery.token и записав его в файлах wasm и abi, которые мы будем использовать позже для настройки контракта токена в нашей собственной учетной записи
```
cline get code inery.token -c token.wasm -a token.abi --wasm

output :

code hash: ac6fb455ea0a66a42fa9bc9aef61cfaf18109346795142194fe3b167dea3a97c сохранение wasm в токен.я сохранял abi в token.abi
```

```
cline set code token22 token.wasm


output :

Reading WASM from /root/token.wasm... Setting Code... executed transaction: 31b009ed4010adc959e1d5177be0213a4a39e87668fe01f3e8ed076c5827a12d 7536 bytes 2281 us # inery <= inery::setcode {"account":"token22","vmtype":0,"vmversion":0,"code":"0061736d0100000001a0011b60000060017e0060027f7f...

cline set code token22 token.abi


output :

Setting ABI... executed transaction: 11e840da507ac57c76bdcef13114cb7922037d7d53e53cf8685def8d9e7c4d0d 1280 bytes 390 us # inery <= inery::setabi {"account":"token22","abi":"0e696e6572793a3a6162692f312e310008076163636f756e7400010762616c616e636505...
```

#### Создать валюту
```
cline push action inery.token create '["<name>", "500000.0000 <name_token>" , "creating my first tokens"]' -p <name>


output:

executed transaction: 8492c49f08d37a5522409ccbd73b7c3f61751639cad08d981135936dd61567aa 120 bytes 163 us # token22 <= token22::create {"issuer":"token22","maximum_supply":"10000.0000 TST"}
```

#### Эмиссионный токен
```
cline push action token22 issue '["token22", "1000.0000 TST", "memo for issuing"]' -p token22
```

#### Transfer
```
cline push action token22 transfer '["token22", "test22", "1.0000 TST", "Here you go 1 TST :) "]' -p token22

output:

executed transaction: 5c820fef47c42a5b056d1b60741000aa90b621e89edf621bbf35c1263b9a460e 136 bytes 223 us # token22 <= token22::transfer {"from":"token22","to":"test22","quantity":"1.0000 TST","memo":"memo"} # test22 <= token22::transfer {"from":"token22","to":"test22","quantity":"1.0000 TST","memo":"memo"}
```

#### Check
Проверьте баланс токена TST для учетной записи

Получить баланс токена TST для токена контрактного счета 22

```
cline get currency balance token22 token22 TST

output :

999.0000 TST
```
Проверьте баланс токена TST для учетной записи test22

```
cline get currency balance token22 test22 TST

output :

1.000 TST
```
