![image](https://user-images.githubusercontent.com/57448493/207617542-b8894f17-2cc4-45e8-bce4-857d4ea73214.png)


#### Что такое Namada?
Кубическое подтверждение приема (CPoS) от Namada
Механизм PoS от Namada называется Cubic Proof-of-Stake (CPoS). Вот некоторые из нововведений CPoS, которые актуальны для валидаторов и делегатов:

Модернизированный вариант механизма распределения вознаграждения F1: Вознаграждения за ставку начисляются автоматически, устраняя необходимость совершать транзакции для получения вознаграждения за ставку и повторной ставки. Это обеспечивается с помощью данного алгоритма, заимствованного из первоначального исследовательского документа F1 Fee Distribution.
Улучшенные гарантии PoS: стоимость атаки на Namada поддается количественной оценке во всех случаях благодаря механизму автоматического определения того, какие учетные записи способствовали возникновению сбоя (валидаторы, делегаторы и т.д.).
Плата за транзакции в нескольких активах: плата за транзакции может вноситься во многих токенах, а какие токены принимаются, может быть обновлено с помощью голосования руководства.

Сейчас можете подать заявку на участие в бесплатной когорте, подробности : https://www.reddit.com/r/Namada/

#### 1. Сборка и внесение вклада из исходного кода
Сначала вам нужно будет установить некоторые зависимости. На системах на базе debian вы можете использовать:
```Shell
sudo apt update && sudo apt install -y curl git build-essential pkg-config libssl-dev
```
После этого вам нужно будет установить Rust, введя следующую команду:
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
