Grafana - это платформа с открытым исходным кодом для визуализации, мониторинга и анализа данных.

Для начала нужно установить Node Exporter

Открываем сессию с сервером, где установлена какая-нибудь нода, откуда вы хотите получать данные в дашборд и устанавливаем на этот сервер ПО:

#### обновляем 'базу данных'
```
sudo apt-get update && sudo apt-get upgrade -y
```
#### скачиваем 'node_exporter' и наводим порядок одной командой
```
cd $HOME && \
wget https://github.com/prometheus/node_exporter/releases/download/v1.2.0/node_exporter-1.2.0.linux-amd64.tar.gz && \
tar xvf node_exporter-1.2.0.linux-amd64.tar.gz && \
rm node_exporter-1.2.0.linux-amd64.tar.gz && \
sudo mv node_exporter-1.2.0.linux-amd64 node_exporter && \
chmod +x $HOME/node_exporter/node_exporter && \
mv $HOME/node_exporter/node_exporter /usr/bin && \
rm -Rvf $HOME/node_exporter/
```
#### создаём файл сервиса 'exporterd'
```
sudo tee /etc/systemd/system/exporterd.service > /dev/null <<EOF
[Unit]
Description=node_exporter
After=network-online.target
[Service]
User=$USER
ExecStart=/usr/bin/node_exporter
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```
#### запускаем сервис 'exporterd' одной командой
```
sudo systemctl daemon-reload && \
sudo systemctl enable exporterd && \
sudo systemctl restart exporterd
```
#### проверяем логи
```
sudo journalctl -u exporterd -f
```
В выводе терминала вы увидите нечто подобное:


Теперь переходим в браузере на своём домашнем устройстве по адресу: http://<server_IP>:9100/, где <server_IP> - IP сервера, на котором установлен node_exporter.

Отобразится Node Exporter. Нажимаем Metrics:


Здесь мы увидим десять тысяч метрик, которые будут отправляться на ноду-обработчик и в дальнейшем отображаться в графане. Отлично.

Далее нам понадобится установить ноду для обработки данных

Нам потребуется сервер, куда будут отправляться данные для обработки и визуализации со всех наших серверов с нодами.

Я возьму сервер с 1 CPU, 1 GB RAM и 20 GB памяти на борту. Если этого окажется мало, то в будущем я обновлю требования.

Открываем сессию с нашим новым сервером (нода-обработчик) и устанавливаем необходимое ПО:

#### обновляем 'базу данных' 
```
sudo apt-get update && sudo apt-get upgrade -y
```
#### устанавливаем 'prometheus' одной командой
```
wget https://github.com/prometheus/prometheus/releases/download/v2.28.1/prometheus-2.28.1.linux-amd64.tar.gz && \
tar xvf prometheus-2.28.1.linux-amd64.tar.gz && \
rm prometheus-2.28.1.linux-amd64.tar.gz && \
mv prometheus-2.28.1.linux-amd64 prometheus
```
#### открываем конфиг прометеуса для настройки
```
nano $HOME/prometheus/prometheus.yml
```
В графу tagrets нам нужно после запятой добавить IP-адрес нашего сервера с нодой, откуда экспортируется статистика.

В будущем их можно будет добавить бесконечно много, но сейчас ограничимся одним сервером.


Сохраняем изменения и создаём сервис.

#### выдаём права на исполнение
```
chmod +x $HOME/prometheus/prometheus
```
#### создаём файл сервиса 'prometheusd'
```
sudo tee /etc/systemd/system/prometheusd.service > /dev/null <<EOF
[Unit]
Description=prometheus
After=network-online.target
[Service]
User=$USER
ExecStart=$HOME/prometheus/prometheus \
--config.file="$HOME/prometheus/prometheus.yml"
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```
#### запускаем сервис 'prometheusd' одной командой
```
sudo systemctl daemon-reload && \
sudo systemctl enable prometheusd && \
sudo systemctl restart prometheusd
```
#### проверяем логи
```
sudo journalctl -u prometheusd -f
```
Переходим в браузере на своём домашнем устройстве по адресу: http://<server_IP>:9090/, где <server_IP> - IP сервера, на котором установлен prometheus.


Переходим в Status > Targets:



Прометеус показывает 2 цели: первая - сервер, где мы устанавливали node_exporter, 2 - сам прометеус (localhost). Отлично.

Установка Grafana

Теперь на этот же сервер с прометеусом мы устанавливаем графану, чтобы как-то визуализировать всю собирающуюся статистику.

#### устанавливаем графану одной командой
```
sudo apt-get install -y adduser libfontconfig1 && \
wget https://dl.grafana.com/oss/release/grafana_8.0.6_amd64.deb && \
sudo dpkg -i grafana_8.0.6_amd64.deb
```
#### запускаем сервис 'grafana-server'
```
sudo systemctl daemon-reload && \
sudo systemctl enable grafana-server && \
sudo systemctl restart grafana-server
```
#### смотрим логи
```
sudo journalctl -u grafana-server -f
```
Переходим в браузере на своём домашнем устройстве по адресу: http://<server_IP>:3000/, где <server_IP> - IP сервера, на котором установлен prometheus и grafana.

Если всё прошло удачно, нас встречает приветственное окно графаны:


Пароль: admin Логин: admin

Теперь задаём собственный пароль:


Следующим шагом станет добавление Data Sources. Выбираем Prometheus:



Указываем в графе URL адрес к своему серверу с прометеусом и графаной:


После чего нажимаем Save & test.

Если всё настроили правильно - отобразится зелёная галочка:


Теперь нам нужно импортировать дашборд, чтобы наконец-то увидеть графики:



Чтобы импортировать дашборд по ID, нам, прежде всего, этот ID нужно узнать.

Переходим к каталогу дашбордов и выбираем любой понравившийся.

К сожалению, не все дашборды из каталога будут работать, но, если потрудиться, то можно найти рабочие решения. Далее в статье я представлю свой дашборд.

Для примера я укажу ID - 1860.


Выбираем имя для дашборда и источник прометеуса

