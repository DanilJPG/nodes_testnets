## Nois
Discord: 
Если ваш бот имеет rpc = http://node-0.noislabs.com:26657/, пожалуйста, измените его на https://rpc.noislabs.com:443/, потому что первый url больше не доступен.

#### Обновление и установка зависимостей 
```Shell
apt-get update 
sudo apt install curl build-essential git wget jq make gcc tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```
#### Установка GO
```Shell
ver="1.19.1"
cd $HOME
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
```
#### Копирование репозитория
```Shell
git clone https://github.com/noislabs/full-node.git 
cd full-node/full-node/
./setup.sh
mv out/noisd $HOME/go/bin/
export PATH=$HOME/go/bin:$PATH
```
#### Инициализация
```Shell
noisd init <name_moniker> --chain-id nois-testnet-002
```
Замените '<name_moniker>' на название своего оператора
#### Скачиваем genesis
```Shell
wget -qO $HOME/.noisd/config/genesis.json "https://raw.githubusercontent.com/noislabs/testnets/main/nois-testnet-002/genesis.json"
```
#### Правим конфиг 
```Shell
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.noisd/config/config.toml

SEEDS=""
PEERS="19ebc9767308ff076bf470070d04746290b3e24b@198.244.179.62:26656,0b77ae4e52c21d01ace4e2371a636bd7e35cea69@135.181.176.109:59656,6f37739579a37e0a1ffe071301d72abe41b17451@158.101.209.61:26656,7444d776b4a092d6631249d456cac4090627e2ae@65.108.194.40:26456,3d1ac72984f3b2f46264689a15e415aed6a06166@195.201.197.4:30656,0983608d136058602a623d0198b3e7e1fbf2c989@38.242.198.130:36656,4f0db2c5875df57df6421b87ae99f74574d2e9a7@78.107.234.44:35656,b7f94fe0324d46be8407fe67dcf80dc67e4627a8@194.163.185.188:26656,f7c96e59081dcac1605faa212295f69bde45927f@185.229.226.252:10656,380d8e530bc16ce2df2c435b9529ad20d5495f22@161.97.151.40:33656,33d1ffc0d531d2419227fb777f81872b8ce07ec9@65.108.206.56:10656,cd9dac4178ac398f2ea47ed8f3555701c5f44af1@159.203.12.167:10656,e7a2c43547de2010e66e473c865d0666067d6ecb@38.242.128.88:12656,ebbb1455a5fd9f72beadaf3e0bf338483733968e@162.55.27.100:30656,2b51fc6354f2ce0fdd1bded54e63e8ac15cae797@207.180.206.3:10656,90a53f3365b3e991aa3d9d7d34bf963ea74120c4@168.119.165.139:26656,443797491a0943838527f2c846f382621e410738@65.108.43.227:26656,ac9a671da9d1703a621b28c0bed024fcfc44f5e7@5.161.84.0:36656,44b7acd072e4ed3db0d41237befb602f1fecb2f0@49.12.236.218:26656,8bd96456e1593e7b062b6001609f327a853fb5b4@65.108.81.83:26656,8538d1adef3ecb541c73167384cf722f921163a4@162.55.235.69:33656,da2945fede11a6726b6d92d21c6e7138bcffd15f@67.205.142.207:10656,a0237167743bd56d23f7bf48c052d1ffd4564e91@65.21.48.222:10656,6d584f7501018441efa3815ad4d50619075708f0@65.108.195.235:13656,a87dc8b4e827a05fe5c46aea54999120c8252587@54.37.6.16:26656,88727530b242f837533d263bc873ca19dc9f3072@89.179.33.100:26656,7a891db83683aee4766cf05ae88c6114aeeac47b@157.90.225.108:26656,09abc7a43c626c668367633c0dd5f25da7a8deef@65.109.55.186:10656,cc1a9320eaf1899dd0ffe4b226d729ab00c4b57d@178.211.139.124:10656,c26a53b97a974e5c8dd81d131c3aacb4da0d8f99@173.249.22.168:10656,8073bd66d5fa581c7b3d0a08d0df1fe318d70d99@135.181.35.46:55656,04180c824f30c10a5a2e5430b7e6158fd46ed3fa@167.235.137.16:10656,5ccd553827dcabc4c48d23228a9875c2f5366028@65.108.241.219:10656,ed1663ad104366cc7de9460a74ff799d5d55f6dd@65.108.103.86:10656,e2b12f0013a032a66c1b18ea0143937ac62c98fc@62.171.142.94:26656,e59e316ea8be3b0582bb79346eb74624c4e4ddb6@161.97.160.86:55656,3dadb0d6975d1b1b456a59403be0f4d6ca9e4ab2@164.92.156.241:10656,d1b820dab0c4542f1e2a599f08800c725cd991ef@38.242.133.200:10656,e693ab5576d6026e3b7100edfd0716b4fb2e19bd@143.110.228.56:10656,bba8023d7d990d2ba7469f6afbe8367b73f57387@161.97.158.155:26656,730522c5db98a432d259d8efb495862b82ffdcbe@178.18.244.98:55656,6185adfb45beb77d9087dd32ada17c293c68be6e@135.181.116.109:26656,4e7c0d78786ed7a22c682a57d0b1d5577277aa53@149.102.134.38:10656,362bf2ffbb5db921d7cf80520150669ebc31cd79@38.242.213.160:40656,397b26d620fb7e9fde7d076cc37911851f796b96@95.217.180.108:26656,e318f4a6a4e55f28a6c99c19221438d1342229f3@5.161.83.230:26656,edc9fd69a299d95ffb0777618ab64c6e9cb39e67@62.171.156.113:10656,3d016efb24c4522155baf90645edbe58ff9fbb75@199.247.6.215:10656,2211b72151868c7299142a452257f7dc2886de2e@142.132.182.1:14656,4d48a6def049818ed5142b17c25293b6e7f08e75@138.201.59.106:26656,1d5b6d33cde619d0073716f65492a4af3735d745@157.90.24.215:10656,3c03522288066493d35849394786591bd34b7ba0@80.254.8.54:26656,079f1674b1c4c4f5f9929fb7a0076d4e197a41f0@135.181.156.2:23656,1bc81a01c2ab7beaddbce295bf8d30710d844cf0@195.3.222.161:30656,60b5e69d15bc2fb20edc12c0c40413343725c54f@65.21.246.152:32656,7bc94e6463eacadb6670c9f8f971b99eb75ec381@38.242.212.47:10656,e0c663bf0482d7c85cd0b154b8fbd4a013b2432b@157.90.226.117:26656,e85038846f7fbf52c904ef7a7ce1fcd801e45278@38.242.141.12:10656,282e1803bf04497b3d4839fdd5047b43971165d9@185.205.244.21:30656,ac05c0fcaf0fdeea870df43853cb48fbc1bc3030@194.163.189.114:46656,b61e8b2e7a658f544da87d264838e1b9db8f3736@146.19.24.52:30656,c56d227ed0cd0474d2797425462a8b508f20f7ab@65.108.137.92:26656,ccc6e92f2edc416c7ed4c69403331d5965565837@38.242.151.87:56656,698075c1ca81ccf763e4a505445747b405a043d6@95.217.133.107:26656,557c41fffcbab57033e92c7a3c595f7822a1014e@38.242.148.59:26656,e9127cf1d726c856e128d08220de948e84e175e1@62.171.171.50:10656,be385046ac405460b4939b7a303797813f4fc904@65.109.1.209:22656,11b3f2dd224af006aca3f9b6525d1b952e12987a@185.182.184.8:10656,2d0fa671089f783cd0bf8c80fde5819fbdce5e69@38.242.222.253:26656,35a86b452c794ed112840378aff5ca4a6a69fe0a@154.12.228.189:26656,48d87a6669ffee319f364795fb220ed086a01f55@35.153.71.164:26656,4789939da9d624d3159792c2990160d9bc0990f9@38.242.134.57:26656,e9af50198f07fd1cdbca685ed6f4376e8b3ad280@38.242.236.188:26656,f0a195f9c4ce70535c67b24dc6f9d3b40f28460b@5.161.78.48:33656,352efa85c0004084f2b7a8b92bc037090c856ade@178.250.246.99:27656,bb82b2949dd196cb938a30df1077a07c43e8baa1@38.242.244.189:30656,c0caed2596821b312080ac7b3dbede9d7985184a@95.216.209.48:26656,0bf9092e8e8e9b38da9ceab5b31ed211b5306be3@66.94.108.7:30656,ffbcaaee6f9bb2967ca9e95304543a2fd098434f@104.248.134.14:10656,585575ebeadd8ab8cca992e2c387303673a288d4@135.181.248.69:36656,4382603c63a88f2497a7c80fbc485f45c4b13ebd@148.251.49.49:32656,2e02d6909b1778ecd138ef0cb81c2e4256ed5015@173.212.223.37:10656,93ced80f0426c373802a6e6538b501f5af3bbf78@2.58.82.107:10656,ea0dc29f0499ce8ea9d7e84cefebf9fe52ebc361@85.10.203.117:56656,93468ebca2604030b19603a251b647b51a3b2153@38.242.241.111:26656,3e3021114400105bed4c040e93d37aeffe71aa6f@104.198.22.115:10656,98fafe4829c568ce1b36d70af050129a388b2b84@178.62.224.156:10656,42ec41fa1a732aece95e7c1129c1758ffe829cae@38.242.229.138:10656,e1562b95c87a696edd22b2a1b0e95072cc3a0af7@38.242.213.161:40656,3518f857f10955a1d939387af94e6f027c5048ad@138.197.148.132:10656,0cfb02d6df8ff6c62a9c6cce5d1f70aef0bb3d53@168.119.114.99:10656,30c4a61abf0d3d8564c9ec6b05627baa5095aefb@149.102.147.157:10656,c79d78f77466abfb30741bff7884ab781b37ae15@195.201.126.156:31656,800a41467ee9057f35d5758466ce6692679609dd@65.108.43.116:56122,59503a2f7be83a093a724d1935e6e9f9d3b16ae3@202.61.194.254:26656,084874565a19e508ec9a798c21428490cc806ed1@84.54.23.195:36656,0195201395c9012c67df1c54fa7cd8c47f1c3cc0@38.242.150.173:56656,f0858944f62345453243c8a6f54efe56c08e8852@168.119.166.51:33656,da5561c6cc72b666cc8e15386790ada9cb664b7b@149.102.136.184:12656,76049cf6c729a15a456a2343ccd2fe22f3728c93@154.53.56.176:26656,32db5114df24e693cc1520f1adde9a0c12a1854c@34.170.72.42:10656,ab92009e773d3d7e8b464b73340164540b506f11@159.223.54.69:26656,3b545c5c67b5997b235d25a02858ae1bcb5d6e37@149.102.159.127:10656,2e398e2188a973b53560c01826775e9d47ecc446@75.119.153.69:26656,a55d5ce23585fadf7fe495b09ae33225049b8963@95.216.213.171:29656,c3e4ce91fece66334f4649cac0325068d6900df2@65.108.79.57:52656,cb64f0fd9c0b680addd3a872ada733a20fb03b8b@65.108.58.240:24656,85bd3253cd92cf566c71c2d180aedb3f3e7c789e@65.21.241.128:26656,7de338f75245c145d2c5c86e709d22f523735a1b@65.108.216.229:26656,38580721b2325e281e9cb082522e6cba9ab5553f@213.136.88.28:10656,fe1e14f13d47a93668cf45b4cbb159db035f4689@162.55.223.152:26656,118bdaf2d346629ce14c364e6bc213dc079b1d74@173.249.9.48:26656,aeebc6927f1fe63d9b8394f8f3da082854ef694e@95.216.212.202:32656,ca31c5613ae5692619cda0d71063b02353587dbe@85.173.112.154:20656,a19205639a4c42ab0bb46d79f4317b7f3380f9f2@185.144.99.19:26656,ef2ff3f17a7f451b686b6653d753eaaf3f6f4b14@88.198.227.30:26656,cd5f2b7e4829561f10bff9e1704491f56a539717@188.34.191.206:26656,b11f7e034486bec2aa24d4cd3cfb0a320f16b4dd@65.109.32.85:55656,58444eab58608a06a650a5837bacc23a4295aa83@45.61.161.116:47023,cdded2b5faa0eaf1aa0770638d7202351d3eb123@176.9.146.72:55656,b542bc78ad7a30023ddc201e7f0f27d7a24f61eb@38.242.150.142:56656,0672033dba94779bbcb07d9af4438c90e76fec9b@161.97.116.150:26656,bf855d10c105e7e80df0f6115154372810a44276@95.111.232.59:10656,10388dc66ea4e57bc70cda45f2dcc17ea7b19c5f@95.216.190.86:10656,da4c74335fd68989ca84ce445ab94165b08f1ab2@65.21.104.130:26656,e50da7ebd7120369294483b5ae0ea92daa25afb6@80.92.204.249:26656,1271aa2a6ff8e7fedf2311e35fff160a92a011e0@5.161.84.1:26656,99ad25781c62b495d2e6166215452a45e1701e47@195.201.165.123:21036,1566d2a2382232c81db92e7e32d2af95a1bc8f25@49.12.197.48:26656,9c614ea2d531739de90eda640c7c7afc0332111e@135.181.144.176:10656,20c7cc01eabbc76ecb88dc568c8629829b22740b@146.19.24.56:26656,93359bff59a0ececa2f9235858668aa508a83cb2@212.90.120.88:32656,1c9b8697c5d44005f89931528b2b929011dea0d2@144.91.113.237:10656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.noisd/config/config.toml

noisd config chain-id nois-testnet-002
noisd config keyring-backend test
noisd config node tcp://localhost:${NOIS_PORT}657

sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${NOIS_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${NOIS_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${NOIS_PORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${NOIS_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${NOIS_PORT}660\"%" $HOME/.noisd/config/config.toml
sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${NOIS_PORT}317\"%; s%^address = \":8080\"%address = \":${NOIS_PORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${NOIS_PORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${NOIS_PORT}091\"%" $HOME/.noisd/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.noisd/config/config.toml
```

#### Сервисный файл
```Shell
sudo tee /etc/systemd/system/noisd.service > /dev/null <<EOF
[Unit]
Description=noisd
After=network-online.target

[Service]
User=$USER
ExecStart=$(which noisd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
#### Запуск
```Shell
sudo systemctl daemon-reload
sudo systemctl enable noisd
sudo systemctl restart noisd && sudo journalctl -u noisd -f -o cat
```
#### Создаем кошелек пока идет синхронизация 
```Shell
# создать кошелек
noisd keys add <name_wallet> --keyring-backend os

# восстановить кошелек (после команды вставить seed)
noisd keys add <name_wallet> --recover --keyring-backend os
```
Идем в дискорд и пользуемся краном 
#### Валидатор
```Shell
noisd tx staking create-validator \
--chain-id nois-testnet-002 \
--commission-rate 0.05 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.1 \
--min-self-delegation "1000000" \
--amount 1000000000000000000unois \
--pubkey $(noisd tendermint show-validator) \
--moniker "<name_moniker>" \
--from <name_wallet> \
--fees 5000unois
```
#### Полезные команды
```Shell
# проверка высота блоков
noisd status 2>&1 | jq ."SyncInfo"."latest_block_height"

# проверить логи
journalctl -u noisd -f -o cat
journalctl --lines=100 --follow --unit noisd

# проверить статус синхронизации
curl localhost:26657/status

# проверить баланс
noisd q bank balances <address>

# вывести список кошельков
noisd keys list
```
#### Валидатор 
```Shell
# собрать реварды со всех валидаторов, которым делегировали (без комиссии)
noisd tx distribution withdraw-all-rewards --from <name_wallet> --fees 5000unois -y

# собрать реварды c отдельного валидатора или реварды + комиссию со своего валидатора
noisd tx distribution withdraw-rewards <valoper_address> --from <name_wallet> --fees 5000unois --commission -y

# заделегировать себе в стейк еще (так отправляется 1 монетa)
noisd tx staking delegate <valoper_address> 1000000unois --from <name_wallet> --fees 5000unois -y

# ределегирование на другого валидатора
noisd tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000unois --from <name_wallet> --fees 5000unois -y
```
#### Edit validator 
```Shell
BINARY tx staking edit-validator \
  --chain-id "CHAIN_NAME" \
  --moniker "MONIKER" \
  --identity "IDENTITY" \
  --details "DETAILS" \
  --from "WALLET_NAME"
```
