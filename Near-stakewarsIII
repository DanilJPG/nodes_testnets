# near_starwarsIII
## Установка и выполнение челленджей 
![image](https://user-images.githubusercontent.com/57448493/182658062-ce6af85c-e704-4301-b20f-665322ac4487.png)


Near Protocol запускает третий эпизод тестнета Stake Wars, целью которого является децентрализация сети NEAR, путем привлечение новых валидаторов. Анонсированные награды составляют 50к $NEAR на ноду в мейнете и 500 $NEAR токенов за успешное выполнение всех заданий. Думаю, описывать что такое Near Protocol не имеет смысла, поэтому читаем информацию о тестнете, подаем заявку на участие по https://nearprotocol1001.typeform.com/to/Z39N7cU9?typeform-source=near.org. В данном гайде описано выполнение заданий. Удачи и да прибудет с Вами Web3. 

#### Список заданй: https://github.com/near/stakewars-iii/tree/main/challenges
#### Форма с выполнением КАЖДОЙ задачи : https://docs.google.com/forms/d/e/1FAIpQLScp9JEtpk1Fe2P9XMaS9Gl6kl9gcGVEp3A5vPdEgxkHx3ABjg/viewform

#### Сайт: https://nearcon.org/
#### Discord : https://discord.gg/bca7SHNM
#### Twitter : https://twitter.com/nearprotocol
#### Ссылка на Hetznet : https://hetzner.cloud/?ref=NY9VHC3PPsL0  

### Установка на сервер и инициализация
Рекумендуемые параметры сервера: CPU: 4-Core, RAM: 8GB DDR4, Storage: 500GB SSD
Сервер на котором буду работать:CPU: 8-Core, RAM: 16GB DDR4, Storage: 240GB SSD 

![image](https://user-images.githubusercontent.com/57448493/182022708-27d272e1-1c2b-46f8-a0d4-c2624dc4e15d.png)

#### Стоимость 26,88 Эвро. Главное следить за использованием  вашей памяти.

### Процесс выбора сервера на Hetzner:

#### 1)Выбираем имя для нашего проекта
![image](https://user-images.githubusercontent.com/57448493/182022753-0d34cf62-a31a-4099-8e96-df27fcf5b65e.png)

#### 2) Выбираем конфигурацию сервера:
![image](https://user-images.githubusercontent.com/57448493/182022795-7847f2ff-48eb-4c63-8408-848623132193.png)
![image](https://user-images.githubusercontent.com/57448493/182022820-05a47a12-4f7b-41f7-a06b-c41e32b823c7.png)

#### 3) Скачиваем MobaExterm
![image](https://user-images.githubusercontent.com/57448493/182022908-ee96955d-3bb4-4412-a2ae-8a6385e54229.png)

#### 4) Выполняем установку и заходим, переходим в Session, выбираем вкладку SSH, в remote host вводим наш IP адрес который пришел нам на почту,которую мы регистрировали
![image](https://user-images.githubusercontent.com/57448493/182022996-3f26c0d9-4436-430c-8873-9bbd8959c110.png)

![image](https://user-images.githubusercontent.com/57448493/182023082-4d1c6a95-ff54-4fda-b316-e853094ec1b2.png)

#### 5) Логинимся:вводим имя root,ввводим пароль который указан в письме, создаем новый пароль
![image](https://user-images.githubusercontent.com/57448493/182023165-1f204414-1c35-4407-b5a3-ba2160f91910.png)
![image](https://user-images.githubusercontent.com/57448493/182023172-f8803754-2ce2-4c03-9a6c-ae03ba74fb1d.png)
![image](https://user-images.githubusercontent.com/57448493/182023184-0c5a4cad-a7bb-4cc1-ab63-41e18c961ed1.png)
![image](https://user-images.githubusercontent.com/57448493/182023192-91c440ca-94e3-43d4-9e43-2134ee059113.png)

### Challange 1
### 6) Перед началом работы вы можете убедиться, что на вашем компьютере установлены правильные функции процессора(ожидаем Supported):

```
lscpu | grep -P '(?=.*avx )(?=.*sse4.2 )(?=.*cx16 )(?=.*popcnt )' > /dev/null \
  && echo "Supported" \
  || echo "Not supported"
```

### 7) Обновляем систему:

```
sudo apt update && sudo apt upgrade -y
```

### 8) Устанавливаем инструменты разработчика, Node.js, npm и другие необходимые пакеты
```
sudo apt install -y git binutils-dev libcurl4-openssl-dev zlib1g-dev libdw-dev libiberty-dev cmake gcc g++ python docker.io protobuf-compiler libssl-dev pkg-config llvm cargo
sudo apt install clang build-essential make
sudo apt install curl jq
curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -  
sudo apt install nodejs
PATH="$PATH"
```
если у вас возникли проблемы с установкой docker.io на ubuntu, попробуйте выполнить эту команду:

```
apt-get install containerd=1.3.3-0ubuntu2
```

### 9) Установить NEAR-CLI
#### Вот репозиторий Github для NEAR CLI.: https://github.com/near/near-cli . Для установки NEAR-CLI, если вы не вошли в систему с правами root, что не рекомендуется, вам нужно будет использовать sudoдля установки NEAR-CLI, чтобы сохранить двоичный файл near в /usr/local/bin
```
sudo npm install -g near-cli
```

### 10) Настраиваем окружение. Текущий тестнет проходит в сети shardnet. Вводим название сети в качестве переменной

```
export NEAR_ENV=shardnet
echo 'export NEAR_ENV=shardnet' >> ~/.bashrc
```
#### Вы также можете выполнить эту команду, чтобы установить постоянную среду Near testnet:

```
echo 'export NEAR_ENV=shardnet' >> ~/.bashrc
echo 'export NEAR_ENV=shardnet' >> ~/.bash_profile
source $HOME/.bash_profile
```

#### Можно проверить корректную работу Near-CLI выполнив руководство по командам NEAR-CLI:

#### Предложения
#### Предложение валидатора означает, что он хочет войти в набор валидаторов, для того чтобы предложение было принято, оно должно соответствовать минимальной цене места.

#### Команда:
```
near proposals
```
#### Текущие валидаторы
#### Здесь отображается список активных валидаторов в текущую эпоху, количество произведенных блоков, ожидаемое количество блоков и скорость онлайн. Используется для мониторинга, если у валидатора возникают проблемы.

#### Команда:
```
near validators current
```

#### Валидаторы следующей эпохи (12 часов)
#### Здесь показаны валидаторы, предложение которых было принято одну эпоху назад, и которые войдут в набор валидаторов в следующую эпоху.

#### Команда:
```
near validators next
```
### Challange 2
### 11) Установка Python pip:
```
sudo apt install python3-pip
```
#### Задаем конфигруацию

```
USER_BASE_BIN=$(python3 -m site --user-base)/bin
export PATH="$USER_BASE_BIN:$PATH"
```

### 12) Устанавливаем Rust и установка Building env

#### устанавливаем необходимые пакеты
```
sudo apt install curl build-essential gcc make -y
```
#### загружаем установочный скрипт Rust
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
#### Вы увидите следующее:
#### Выбираем пункт 1) продолжить установку (по умолчанию).
![image](https://user-images.githubusercontent.com/57448493/182024420-7339d623-2e61-4072-ac68-c408f5e59436.png)

#### активируем среду Rust для текущей оболочки
```
source ~/.profile
source ~/.cargo/env
```

### 13) Заходим по ссылке https://wallet.shardnet.near.org/ и создаем кошелек NEAR для тестовой сети. Сохраняем мнемонику в надежном месте. Запоминаем имя кошелька, он же Account_ID.
![image](https://user-images.githubusercontent.com/57448493/182023611-9f7ad21f-472f-43ce-8040-b47ef30c63cb.png)
### Клонируем репозиторий с нодой
```
git clone https://github.com/near/nearcore
cd nearcore
git fetch
```
#### Проверяем коммит. По состоянию на 31 июля, актуальный коммит - c1b047b8187accbf6bd16539feb7bb60185bdc38. Актуальный коммит можно найти по ссылке https://github.com/near/stakewars-iii/blob/main/commit.md
```
git checkout c1b047b8187accbf6bd16539feb7bb60185bdc38
```

### 14) Скомпилировать 'nearcore' двоичный файл

```
cargo build -p neard --release --features shardnet
```
+ Двоичный путь является 'target/release/neard'. Если вы видите проблемы, возможно, команда cargo не найдена. Компиляция 'nearcore' двоичного файла может занять некоторое время(у меня заняло примерно час).

### 15) Инициализируем ноду и загружаем генезис файл

```
./target/release/neard --home ~/.near init --chain-id shardnet --download-genesis
```

![image](https://user-images.githubusercontent.com/57448493/183308533-b74f49e3-7146-4389-b9cb-4b78f8a8d4d6.png)

#### Эта команда создаст структуру каталогов и создаст 'config.json' , 'node_key.json' , и 'genesis.json' в переданной вами сети.

+ 'config.json' - Параметры конфигурации, которые реагируют на то, как будет работать узел. 'Config.json' содержит необходимую информацию для запуска узла в сети, как взаимодействовать с одноранговыми узлами и как достичь консенсуса. Хотя некоторые параметры настраиваются. Как правило, валидаторы предпочитают использовать файл 'config.json' по умолчанию.

+ 'genesis.json' - Файл со всеми данными, с которыми начиналась работа в 'genesis' в сети. Здесь содержатся начальные учетные записи, контракты, ключи доступа и другие записи, которые представляют начальное состояние блокчейна. Файл 'genesis.json' представляет собой снимок состояния сети в определенный момент времени. В контактах учетные записи, балансы, активные валидаторы и другая информация о сети.

+ 'node_key.json' - Файл, содержащий открытый и закрытый ключи для узла. Также включает необязательный 'account_id' параметр, который требуется для запуска узла проверки (не рассматривается в этом документе).

+ 'data/' - Папка, в которую узел 'NEAR' будет записывать свое состояние.

## Рекомендую сохранить данные файлы, чтобы потом не возникло проблем с восстановлением и предоставлением данных, что вы действительно все сделали правильно и получили вознаграждения

### 16) Замените config.json
#### Из сгенерированных 'config.json' есть два параметра для изменения:

'boot_nodes': Если вы не указали загрузочные узлы для использования во время инициализации на шаге 3, сгенерированный 'config.json' массив покажет пустой массив, поэтому нам нужно будет заменить его полным массивом с указанием загрузочных узлов.

'tracked_shards': В сгенерированном 'config.json' это поле является пустым. Вам нужно будет заменить его на "tracked_shards": [0]

```
rm ~/.near/config.json
wget -O ~/.near/config.json https://s3-us-west-1.amazonaws.com/build.nearprotocol.com/nearcore-deploy/shardnet/config.json
```

### 17) Запустите узел
```
cd ~/nearcore
./target/release/neard --home ~/.near run
```

![image](https://user-images.githubusercontent.com/57448493/183308549-269fb5fd-3d10-44d4-a90c-85d0098273e5.png)

+ Теперь узел запущен, и вы можете видеть логи в консоли. Ваш узел должен находить `peers`, загружать `headers` до 100%, а затем загружать блоки.

### 18) Активация узла в качестве средства проверки

#### Авторизовать кошелек локально
#### Для подписи транзакций через NEAR-CLI необходимо локально установить ключ полного доступа.

#### Вам нужно выполнить эту команду:
```
near login
```
#### Примечание: Эта команда запускает веб-браузер, позволяющий авторизовать полный ключ доступа для локального копирования.

1 – Скопируйте ссылку в свой браузер
изображение
![image](https://user-images.githubusercontent.com/57448493/182026199-228db273-b4f8-4fe6-995b-239794ef70a3.png)

2 – Предоставить доступ к Near CLI
![Screenshot_2](https://user-images.githubusercontent.com/57448493/182026211-077f6e98-7c45-488e-b0a9-1a21ffb87ccd.png)

3 – После предоставления разрешения вы увидите страницу, подобную этой, вернитесь в консоль
![image](https://user-images.githubusercontent.com/57448493/183308584-5c43cc0d-7cc6-4981-a727-79f1bf887038.png)
#### Должно быть так ![image](https://user-images.githubusercontent.com/57448493/183308591-5250090d-c71b-497c-9d90-4117bbca204c.png)
#### если возникла такая ошибка, попробуйте сделать снепшот genesis.json, командой 
```
cd ~/.near
wget https://s3-us-west-1.amazonaws.com/build.nearprotocol.com/nearcore-deploy/shardnet/genesis.json
```
![image](https://user-images.githubusercontent.com/57448493/182026311-ac58eef7-b1f8-48b6-9a9f-0dd380f77de4.png)

### Но лучше удалите ноду и повторите все заново,можно создать сначала сервисный файл,а потом уже подключаться к кошельк, самое главное следить за логами

#### Проверьте validator_key.json
 Выполните следующую команду:
```
cat ~/.near/validator_key.json
```
#### Примечание: Если файл validator_key.json отсутствует, выполните следующие действия, чтобы создать его
Создать validator_key.json

Сгенерируйте файл ключа:
```
near generate-key <pool_id>
```
#### <pool_id> ---> xx.factory.shardnet.near ГДЕ xx - это имя вашего пула

### Скопируйте сгенерированный файл в папку shardnet: не забудьте заменить <pool_id> идентификатором вашей учетной записи
```
cp ~/.near-credentials/shardnet/YOUR_WALLET.json ~/.near/validator_key.json
```
#### Отредактируйте 'account_id' => xx.factory.shardnet.near, где xx - это имя вашего пула

#### Изменить private_keyна secret_key
Примечание: идентификатор учетной записи должен совпадать с именем контракта пула ставок, иначе вы не сможете подписывать блоки.\

Содержимое файла должно быть в следующем формате:
```
{
  "account_id": "xx.factory.shardnet.near",
  "public_key": "ed25519:HeaBJ3xLgvZacQWmEctTeUqyfSU4SDEnEwckWxd92W2G",
  "secret_key": "ed25519:****"
}
```
### Запускаем ноду
```
target/release/neard run
```

### 19)Создаем сервисный файл
```
sudo tee /etc/systemd/system/neard.service > /dev/null <<EOF
[Unit]
Description=neard

[Service]
Type=simple
User=root
#Group=near
WorkingDirectory=/home/root/.near
ExecStart=/home/<USER>/nearcore/target/release/neard run
Restart=on-failure
RestartSec=30
KillSignal=SIGINT
TimeoutStopSec=45
KillMode=mixed

[Install]
WantedBy=multi-user.target
EOF
```
#### Start
Команда:
```
sudo systemctl enable neard
```
Команда:
```
sudo systemctl start neard
```
#### Если вам необходимо внести изменения в сервис из-за ошибки в файле. Он должен быть перезагружен:
```
sudo systemctl reload neard
```
#### Смотреть логи
Команда:
```
journalctl -n 100 -f -u neard
```
![image](https://user-images.githubusercontent.com/57448493/183308605-26e904e8-633a-4886-9a87-3e3bf885ccf0.png)

#### Сделайте вывод журнала красивым шрифтом

Команда:
```
sudo apt install ccze
```
#### Просмотр журналов с цветом

Команда:
```
journalctl -n 100 -f -u neard | ccze -A
```
### Стать валидатором
Чтобы стать валидатором и войти в набор валидаторов, необходимо выполнить минимальный набор критериев успеха.

1)Узел должен быть полностью синхронизирован
#### Проверяем синхронизацию
```
curl -s http://127.0.0.1:3030/status | jq .sync_info
```
![image](https://user-images.githubusercontent.com/57448493/183308691-79befeb8-955f-4e4f-b24e-70c2e368eaf9.png)

2) `validator_key.json` должен быть на месте
3)Контракт должен быть инициализирован с помощью `public_key` в `validator_key.json`
4) Идентификатор учетной записи должен быть установлен на идентификатор контракта пула ставок
5) Должно быть достаточно делегаций, чтобы соответствовать минимальной цене места. Посмотреть стоимость места можно здесь(https://explorer.shardnet.near.org/nodes/validators).
6) Предложение должно быть отправлено путем пинга контракта
7) После принятия предложения валидатор должен подождать 2-3 эпохи, чтобы войти в набор валидаторов
8) После установки валидатора валидатор должен выдавать более 90% назначенных блоков

Проверьте статус работы узла валидатора. Если отображается "Validator", значит ваш пул выбран в списке текущих валидаторов.
 
### Challange 3 
### Создаем пул ставок 
Для обеспечения безопасности средств делегаторов NEAR использует фабрику стейкинг-пула с контрактом на стейкинг, прошедшим белую проверку. Чтобы запустить валидатора на NEAR, стакинг-пул должен быть развернут на учетной записи NEAR и интегрирован в узел валидатора NEAR. Делегаторы должны использовать пользовательский интерфейс или командную строку, чтобы делать ставки в пул. Ставочный пул - это смарт-контракт, развернутый на учетной записи NEAR.


#### Развертывем пул ставок командой

### Важно! заменяем `<pool id>` , `<MONIKER>` и `<ACCOUNT_ID>` на значения указанные в 18 пункте. Вместо `<public key>` вставляем ключ из файла validator_key.json
```
near call factory.shardnet.near create_staking_pool '{"staking_pool_id": "<pool id>", "owner_id": "<accountId>", "stake_public_key": "<public key>", "reward_fee_fraction": {"numerator": 5, "denominator": 100}, "code_hash":"DD428g9eqLL8fWUxv8QSpVFzyHi1Qd16P8ephYCTmMSZ"}' --accountId="<accountId>" --amount=30 --gas=300000000000000
```
![image](https://user-images.githubusercontent.com/57448493/183308717-58fdcdac-929a-4e8a-b21a-6286ae591cb2.png)

### Теперь можем застейкать еще токенов. В моем случае 30(минимум)
#### Должно быть примерно так
```
NEAR_ENV=shardnet near call $POOL deposit_and_stake --amount 30 --accountId $ACCOUNT_ID --gas=300000000000000
```
![image](https://user-images.githubusercontent.com/57448493/183308731-ecbf6abe-c2e3-43a7-95dc-0c052836e496.png)
### Challange 4
#### Мониторинг
Заходим по ссылке и ищем нашего валидоатор и проверяем количество застейканных монет:
https://explorer.shardnet.near.org/nodes/validators
![image](https://user-images.githubusercontent.com/57448493/182044145-8a5c91a3-b8d4-4f64-8de0-63683432b5d1.png)


Уведомление по электронной почте может сделать поддержание валидатора в рабочем состоянии более удобным. Станьте валидатором, подтверждающим транзакции в testnet, и получите >95% времени работы.

Файлы журналов
Лог-файл хранится либо в каталоге `~/.nearup/logs`, либо в `systemd`, в зависимости от вашей настройки.

Команда systemd:
```
journalctl -n 100 -f -u neard | ccze -A
```
Пример файла журнала:

Валидатор | 1 валидатор
```
INFO stats: #85079829 H1GUabkB7TW2K2yhZqZ7G47gnpS7ESqicDMNyb9EE6tf Validator 73 validators 30 peers ⬇ 506.1kiB/s ⬆ 428.3kiB/s 1.20 bps 62.08 Tgas/s CPU: 23%, память: 7.4 GiB
```
 Надпись "Validator" указывает на то, что вы являетесь активным валидатором.
73 валидатора: Всего 73 валидатора в сети
30 peers: В настоящее время у вас 30 peers. Для достижения консенсуса и начала валидации вам необходимо как минимум 3 peers.
#46199418: блок - Посмотрите, чтобы убедиться, что блоки движутся

#### RPC
Любой узел в сети предлагает услуги RPC на порту 3030, если порт открыт в брандмауэре узла. NEAR-CLI использует вызовы RPC за сценой. Обычно RPC используется для проверки статистики валидатора, версии узла и просмотра доли делегата, хотя его можно использовать для взаимодействия с блокчейном, счетами и контрактами в целом.

Более подробно о многих командах и их использовании можно узнать здесь:

https://docs.near.org/api/rpc/introduction

Команда:
```
sudo apt install curl jq
```
Общие команды:
#### Проверьте версию вашей ноды: Команда:
```
curl -s http://127.0.0.1:3030/status | jq .version
```
#### Проверьте делегаторов и кол: Команда:
```
near view <ваш пул>.factory.shardnet.near get_accounts '{"from_index": 0, "limit": 10}' --accountId <accountId>.shardnet.near
```
#### Проверить причину срабатывания валидатора:
```
curl -s -d '{"jsonrpc": "2.0", "method": "validators", "id": "dontcare", "params": [null]}'' -H 'Content-Type: application/json' 127.0.0.1:3030 | jq -c '.result.prev_epoch_kickout[] | select(.account_id | contains ("<POOL_ID>"))' | jq .reason
```
#### Блоки / Ожидается Команда:
  ```
curl -r -s -d '{"jsonrpc": "2.0", "method": "validators", "id": "dontcare", "params": [null]}'' -H 'Content-Type: application/json' 127.0.0.1:3030 | jq -c '.result.current_validators[] | select(.account_id | contains ("POOL_ID"))'
 ```
### Challange 6 (Создайте задачу cron на машине, на которой запущен валидатор узлов, которая позволяет автоматически пинговать сеть)
Создайте новый файл в /home/root/scripts/ping.sh
```
#!/bin/sh
# Ping вызывает информацию для обновления Proposal и добавляет в crontab

mkdir logs
mkdir scripts
cat > /root/scripts/ping.sh

export NEAR_ENV=shardnet
export POOL=$POOL
export ACCOUNT_ID=$ACCOUNT_ID
export MONIKER=$MONIKER
export LOGS=/root/logs

echo "---" >> $LOGS/all.log
date >> $LOGS/all.log
near call $POOL ping '{}' --accountId $ACCOUNT_ID --gas=300000000000000 >> $LOGS/all.log
near proposals | grep $MONIKER >> $LOGS/all.log
near validators current | grep $MONIKER >> $LOGS/all.log
near validators next | grep $MONIKER >> $LOGS/all.log
```
#### Создаем новый crontab, запускающий наш скрипт каждые 2 часа
```
crontab -e
```
#### Копируем команду и вставляем в файл
![image](https://user-images.githubusercontent.com/57448493/182424720-df65d548-fd92-4354-8105-9262e1cac5a6.png)

```
0 */2 * * * sh /root/scripts/ping.sh
```
##### Перезапускаем cron
```
systemctl restart cron
```
Проверяем активен ли cron
```
systemctl status cron
```
![image](https://user-images.githubusercontent.com/57448493/182424987-885b5b5c-6f83-4faa-b338-ff2708fddf5f.png)

+ Через пару часов проверяем логи
```
cat /root/logs/all.log
```
+ Для подтверждения задания отправляем в форму скриншот транзакций и ссылку на свой пул
![image](https://user-images.githubusercontent.com/57448493/182425140-1e5841eb-4e54-4620-ac10-4cbe8c286bcc.png)

### Challange 8 ( Разверните смарт-контракт на аккаунте владельца пула ставок)

#### Обновляем Rust
```
curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
source $HOME/.cargo/env
```
#### Добавляем набор wasm32-unknown-unknown
```
rustup target add wasm32-unknown-unknown
```
#### Клонируем репозиторий и компилируем смарт контракт
```
git clone https://github.com/zavodil/near-staking-pool-owner
cd near-staking-pool-owner/contract
```
#### Составление смарт-контракта
```
rustup target add wasm32-unknown-unknown
cargo build --target wasm32-unknown-unknown --release
```
#### Разворачиваем смарт контракт на своей учетной записи
```
NEAR_ENV=shardnet near deploy $ACCOUNT_ID --wasmFile target/wasm32-unknown-unknown/release/contract.wasm
```

#### Создаем новый кошелек, на который будем отправлять часть доходов https://wallet.shardnet.near.org/. Сохраняем мнемонику в надежном месте.

#### Инициализируйте смарт-контракт, подбирающий счета для разделения выручки.
```
CONTRACT_ID=<OWNER_ID>.shardnet.near
```
#### Измените числитель и знаменатель, чтобы скорректировать % для разделения..
```
NEAR_ENV=shardnet near call $CONTRACT_ID new '{"staking_pool_account_id": "<STAKINGPOOL_ID>.factory.shardnet.near", "owner_id":"<OWNER_ID>.shardnet.near", "reward_receivers": [["<SPLITED_ACCOUNT_ID_1>.shardnet.near", {"numerator": 3, "denominator":10}], ["<SPLITED_ACCOUNT_ID_2>.shardnet.near", {"numerator": 70, "denominator":100}]]}' --accountId $CONTRACT_ID
```
#### По прошествии одной эпохи (8 часов) можно вывести токены, используя следующую команду
```
NEAR_ENV=shardnet near call $ACCOUNT_ID withdraw '{}' --accountId $ACCOUNT_ID --gas 200000000000000
```
{} выведет все токены из стейка. После успешной транзакции, застейкать токены заново
Делаем скриншот полученного вывода и прикрепляем его в форму для сдачи заданий вместе с ссылкой на транзакцию.
![image](https://user-images.githubusercontent.com/57448493/183406351-f23a2bbd-7edb-4154-b160-e81039da8a55.png)
### Обновление
#### Останавливаем ноду
```
sudo systemctl stop neard
```
#### Подтягиваем новую версию и собираем бинарные файлы
```
export NEAR_ENV=shardnet
echo 'export NEAR_ENV=shardnet' >> ~/.bashrc
cd ~/nearcore
git fetch
git checkout 68bfa84ed1455f891032434d37ccad696e91e4f5
cargo build -p neard --release --features shardnet
```
Скачиваем новый файл конфига
```
rm ~/.near/config.json 
wget -O ~/.near/config.json https://s3-us-west-1.amazonaws.com/build.nearprotocol.com/nearcore-deploy/shardnet/config.json
```
Если при выполнении команды cargo возникает ошибка версии Rust
![image](https://user-images.githubusercontent.com/57448493/183406749-6d1e0824-0e4f-4923-ac56-8caf6e945482.png)

Обновляем раст и запускаем cargo заново
```
curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
source $HOME/.cargo/env
```
```
cargo build -p neard --release --features shardnet
```
Перезапускаем ноду и ждем окончания синхронизации
```
sudo systemctl start neard && journalctl -n 100 -f -u neard | ccze -A
```
В логах может быть куча сообщений с текстом "missing chunks". Проверяем логи командой, если блоки совпадают с эксплорером и увеличиваются, всё ок.
```
journalctl -n 100 -f -u neard|ccze -A| grep INFO
```
### Удалить ноду
```
sudo systemctl stop neard && \
sudo systemctl disable neard && \
rm /etc/systemd/system/neard.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf .near nearcore && \
rm -rf $(which neard)
```
