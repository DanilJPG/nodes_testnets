## Aleo
 
![image](https://user-images.githubusercontent.com/57448493/201538555-429fed0a-7950-4c4e-9e97-c6f368708b5d.png)
 
Aleo привлек 228 млн. долларов для разработки своего проекта,написанного на собственном языке программирования LEO. Команда стремится к созданию пользовательского интерфейса в Интернете, который является одновременно по-настоящему личным и по-настоящему приватным. С возможность развертыванию приложений в сети с нулевым знанием,что дает безопасность и смягчат сливы приватных данных в сети. Это лишь немного, что представляет собой Aleo. 

Советую ознакомится: https://www.aleo.org/post/discover-aleo

Website: https://www.aleo.org/post/announcing-testnet-3

Discord: https://discord.gg/7scts2cr

Github: https://github.com/AleoHQ/snarkOS/ 

#### 1. Overview
snarkOS - это децентрализованная операционная система для приложений с нулевым уровнем знаний. Этот код формирует основу сети Aleo, которая проверяет транзакции и хранит зашифрованные государственные приложения общедоступным способом.

#### 2. Build Guide
2.1 Requirements

Ниже приведены минимальные требования для запуска узла Aleo:
```
CPU: 16-cores (32-cores preferred)
RAM: 16GB of memory (32GB preferred)
Storage: 128GB of disk space
Network: 10 Mbps of upload and download bandwidth
```
Пожалуйста, обратите внимание, что для запуска конкурентоспособного Aleo Prover машине потребуется нечто большее, чем эти требования.

#### 2.2 Installation
Перед началом работы, пожалуйста, убедитесь, что на вашем компьютере установлена версия Rust v1.64+. Инструкции по установке Rust можно найти здесь.https://www.rust-lang.org/tools/install

Начните с клонирования этого репозитория Github:
```
git clone https://github.com/AleoHQ/snarkOS.git --depth 1
```
Затем перейдите в каталог snarkOS:
```
cd snarkOS
```
[Для пользователей Ubuntu] Доступен вспомогательный скрипт для установки зависимостей. Из каталога snarkOS запустите:
```
./build_ubuntu.sh
```
Наконец, установите snarkOS:
```
cargo install --path /root/snarkOS/
```
#### 3. Run an Aleo Node
3a. Запустите клиент Aleo
Начните с следования инструкциям в руководстве по сборке.

Затем, чтобы запустить клиентский узел из каталога snarkOS, запустите:
```
./run-client.sh
```
#### 3b. Run an Aleo Prover
Начните с следования инструкциям в руководстве по сборке.

Затем сгенерируйте адрес учетной записи Aleo:
```
snarkos account new 
```
Это приведет к созданию новой учетной записи Aleo в терминале.

Пожалуйста, не забудьте сохранить закрытый ключ учетной записи и ключ просмотра. Ниже приведен пример вывода:

 Attention - Remember to store this account private key and view key.
```
  Private Key  APrivateKey1xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx  <-- Save Me And Use In The Next Step
     View Key  AViewKey1xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx  <-- Save Me
      Address  aleo1xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx  <-- Save Me
```     
Затем, чтобы запустить узел проверки, из каталога snarkOS запустите:
```
./run-prover.sh
```
При появлении запроса введите свой закрытый ключ Aleo:
```
Введите закрытый ключ учетной записи Aleo Prover:
APrivateKey1xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

```
snarkos --help
```
```
OPTIONS:
    -h, --help                     Print help information
    -v, --verbosity <VERBOSITY>    Specify the verbosity [options: 0, 1, 2, 3] [default: 2]

SUBCOMMANDS:
    account    Команды для управления учетными записями Aleo
    clean      Очищает хранилище узла snarkOS
    help       Распечатайте это сообщение или справку из данной подкоманды(ов)
    start      Запускает узел snarkOS
    update     Обновление snarkOS
```
