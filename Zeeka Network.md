![image](https://user-images.githubusercontent.com/57448493/192145552-6eed7477-d72a-4089-bf94-172f4deec8ff.png)

Zeeka (ℤ) - это криптовалюта, целью которой является создание легкого и масштабируемого блокчейна с широким использованием технологии доказательства нулевого знания.

Zeeka предлагает новую концепцию под названием "Нулевые контракты". Нулевые контракты являются эквивалентом смарт-контрактов в некоторых основных блокчейн-системах, таких как Ethereum. Эти контракты будут выражены в виде математических ограничений вместо байт-кодов виртуальной машины, такой как виртуальная машина Ethereum.

Zeeka включит концепции, ранее использовавшиеся в качестве решений уровня конфиденциальности или L2 в других цепочках, в ядро нового блокчейна, стремясь создать более масштабируемую сеть с лучшей конфиденциальностью.

На данный момент возможно помогать проекту своими силами. Есть 3 формы для заполнения:

1. Форма для участника комьюнити (на модераторов):https://docs.google.com/forms/d/e/1FAIpQLSdz129RVXCPLIipF2evu5HDblo5iXdVBtk-3RO6XzKYCAVGlQ/viewform

2. Форма контрибуций:https://docs.google.com/forms/d/e/1FAIpQLSewVt8hRnRcufFOLCm9E9tNSeQ9FgWBjmygyIScA6_c5H7NPg/viewform

3. Подача заявки на тестнет:https://docs.google.com/forms/d/e/1FAIpQLSdZVJmcL5X83zDUdRIJxuWiSi8hvmocEM7Ut8E0m97-cmdgcQ/alreadyresponded

Официальный сайт:https://zeeka.io/

GitHub проекта:https://github.com/zeeka-network/bazuka

#### 1. Подготовка сервера
```
sudo apt update && sudo apt upgrade -y
sudo apt install wget jq git libssl-dev cmake -y
```
#### 2.Установить Rust
```
. <(wget -qO- https://raw.githubusercontent.com/letsnode/Utils/main/installers/rust.sh)
```
#### 3.Клонировать репозиторий с нодой
```
git clone https://github.com/zeeka-network/bazuka
```
#### 4.Перейти в папку bazuka,компиляция и установка
```
cd bazuka && cargo install --path
```
#### 5.Инициализировать ноду
```
bazuka init --seed '<Ваша мнемоника от любого кошелька где нет монет>' --network debug --node 127.0.0.1:8765
```
Ожидаем:
![image](https://user-images.githubusercontent.com/57448493/192145821-fe01f241-8795-48d9-b9aa-72b25db18b7e.png)

#### 6.Создать сервисный файл
```
sudo tee <<EOF >/dev/null /etc/systemd/system/zeeka.service
[Unit]
Description=Zeeka node
After=network.target

[Service]
User=$USER
ExecStart=`RUST_LOG=info which bazuka` node --listen 0.0.0.0:8765 --external [IP address]:8765 --network debug --db ~/.bazuka-debug --bootstrap 5.161.152.123:8765 --bootstrap 65.108.201.41:8765 --bootstrap 185.213.25.229:8765 --bootstrap 45.88.106.199:8765 --bootstrap 148.251.1.124:8765 --bootstrap 195.54.41.115:8765 --bootstrap 195.54.41.130:8765
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
Замените [IP address] на ip вашего сервера
```
#### 7.Запустить сервис
```
sudo systemctl daemon-reload
sudo systemctl enable zeeka
sudo systemctl restart zeeka
```
Добавить команду для просмотра лога ноды в систему в виде переменной
```
. <(wget -qO- https://raw.githubusercontent.com/AlexM-dev/Utils/main/commands/insert_variable.sh) -n zeeka_log -v "sudo journalctl -fn 100 -u zeeka" -a
```
#### Посмотреть логи
```
bazuka node --listen 0.0.0.0:8765 --external [Ваш IP  адрес сервера]:8765
zeeka_log
```
![image](https://user-images.githubusercontent.com/57448493/192145776-1ea5c03c-de7a-4b97-8bbb-403e006622d5.png)

Скопируйте данные в надежное место!!!

#### Удалить ноду 
```
sudo systemctl stop zeeka && \
sudo systemctl disable zeeka && \
rm /etc/systemd/system/zeeka.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf .bazuka && \
rm -rf $(which bazuka)
```
