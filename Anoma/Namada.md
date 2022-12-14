
[Discord](https://discord.gg/anoma) | [Website](https://anoma.net/) | [Reddit](https://www.reddit.com/r/Namada/)
--- | --- | ---

Steps | Comments
--- | --- |
[Server Preparation](https://github.com/DanilJPG/nodes_testnets/blob/main/Anoma/Namada.md#:~:text=1.%20%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0%20%D0%B7%D0%B0%D0%B2%D0%B8%D1%81%D0%B8%D0%BC%D0%BE%D1%81%D1%82%D0%B5%D0%B9%20%D0%B8%20%D0%BA%D0%BB%D1%8E%D1%87%D0%B5%D0%B9) | Check the version, if necessary update to the appropriate height blocks,server setup, Rust language is necessary to work with a binary file and unpack it
[Working with the repository](https://github.com/DanilJPG/nodes_testnets/blob/main/Anoma/Namada.md#:~:text=%D0%9A%D0%BE%D0%B3%D0%B4%D0%B0%20%D0%B2%D1%81%D0%B5%20%D0%B1%D1%83%D0%B4%D0%B5%D1%82%20%D1%83%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BB%D0%B5%D0%BD%D0%BE%2C%20%D0%BA%D0%BB%D0%BE%D0%BD%D0%B8%D1%80%D1%83%D0%B9%D1%82%D0%B5%20%D1%80%D0%B5%D0%BF%D0%BE%D0%B7%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%B9%20Namada%20Trusted%20Setup%20Ceremony%20GitHub%20%D0%B8%20%D0%B8%D0%B7%D0%BC%D0%B5%D0%BD%D0%B8%D1%82%D0%B5%20%D0%BA%D0%B0%D1%82%D0%B0%D0%BB%D0%BE%D0%B3%D0%B8%20%D0%B2%20namada%2Dtrusted%2Dsetup%3A) | Clone the repository and the necessary binaries
[Contribution](https://github.com/DanilJPG/nodes_testnets/blob/main/Anoma/Namada.md#:~:text=2.%20%D0%92%D0%BA%D0%BB%D0%B0%D0%B4%20%D0%B8%D0%B7%20%D0%BF%D1%80%D0%B5%D0%B4%D0%B2%D0%B0%D1%80%D0%B8%D1%82%D0%B5%D0%BB%D1%8C%D0%BD%D0%BE%20%D1%81%D0%BE%D0%B1%D1%80%D0%B0%D0%BD%D0%BD%D1%8B%D1%85%20%D0%B4%D0%B2%D0%BE%D0%B8%D1%87%D0%BD%D1%8B%D1%85%20%D1%84%D0%B0%D0%B9%D0%BB%D0%BE%D0%B2%20(%D1%80%D1%83%D1%87%D0%BD%D0%B0%D1%8F%20%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0)) | Manual and automatic contribution 
[Useful commands]() | 
[Possible Errors]() | 

![image](https://user-images.githubusercontent.com/57448493/207617542-b8894f17-2cc4-45e8-bce4-857d4ea73214.png)


#### Что такое Namada?
Кубическое подтверждение приема (CPoS) от Namada
Механизм PoS от Namada называется Cubic Proof-of-Stake (CPoS). Вот некоторые из нововведений CPoS, которые актуальны для валидаторов и делегатов:

Модернизированный вариант механизма распределения вознаграждения F1: Вознаграждения за ставку начисляются автоматически, устраняя необходимость совершать транзакции для получения вознаграждения за ставку и повторной ставки. Это обеспечивается с помощью данного алгоритма, заимствованного из первоначального исследовательского документа F1 Fee Distribution.
Улучшенные гарантии PoS: стоимость атаки на Namada поддается количественной оценке во всех случаях благодаря механизму автоматического определения того, какие учетные записи способствовали возникновению сбоя (валидаторы, делегаторы и т.д.).
Плата за транзакции в нескольких активах: плата за транзакции может вноситься во многих токенах, а какие токены принимаются, может быть обновлено с помощью голосования руководства.

Сейчас можете подать заявку на участие в бесплатной когорте, подробности : https://www.reddit.com/r/Namada/

#### 1. Установка зависимостей и ключей
Сначала вам нужно будет установить некоторые зависимости. На системах на базе debian вы можете использовать:
```Shell
sudo apt update && sudo apt install -y curl git build-essential pkg-config libssl-dev
```
#### После этого вам нужно будет установить Rust, введя следующую команду:
```Shell
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
Если у вас уже установлен Rust, убедитесь, что это самая последняя версия:
```Shell
rustup update
```
Когда все будет установлено, клонируйте репозиторий Namada Trusted Setup Ceremony GitHub и измените каталоги в namada-trusted-setup:
```Shell
git clone https://github.com/anoma/namada-trusted-setup.git
cd namada-trusted-setup && git checkout v1.1.0
```
Соберите бинарный файл:
```Shell
cargo build --release --bin namada-ts --features cli
```
Переместите двоичный файл в $PATH (может потребоваться sudo):
```Shell
mv target/release/namada-ts /usr/local/bin 
```
Запустите свой вклад:
```Shell
namada-ts contribute default https://contribute.namada.net $TOKEN
```
### 2. Вклад из предварительно собранных двоичных файлов (ручная настройка)
Мы предоставляем предварительно собранные двоичные файлы x86_64 для Linux, MacOS и Windows. Для этого перейдите на страницу Releases и скачайте последнюю версию клиента.

После загрузки вам может потребоваться предоставить права на выполнение: chmod +x namada-ts-{distrib}-{version}.

Наконец, запустите клиент с помощью:
```Shell
./namada-ts-{distrib}-{version} contribute default https://contribute.namada.net $TOKEN.
```
#### 3. Внесение вклада из предварительно собранных двоичных файлов (автоматическая установка)
Если вы работаете на Linux или MacOS, мы также предоставляем сценарий установки для автоматизации установки двоичных файлов. Вы можете выполнить следующую команду:
```Shell
curl --proto '=https' --tlsv1.2 -sSf https://raw.githubusercontent.com/anoma/namada-trusted-setup/main/install.sh | sh
```
и вы готовы внести свой вклад:
```Shell
namada-ts contribute default https://contribute.namada.net $TOKEN
```
