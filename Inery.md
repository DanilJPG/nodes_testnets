Hardware Requirements
Component	Minimum Requirement
```
CPU	Intel Core i3 or i5
RAM	4 GB DDR4 RAM
Storage	500 GB HDD
Connection	100 Mbit/s port
```
Component	Recommended Requirement
```
CPU	Intel Core i7-8700 Hexa-Core
RAM	64 GB DDR4 RAM
Storage	2 x 1 TB NVMe SSD
Connection	1 Gbit/s port
```
Software Requirements
Component	Minimum Requirement
```
OS	Ubuntu 16.04
```
Component	Recommended Requirement
```
OS	Ubuntu 18.04 or higher
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

#### Загрузка пакета Inery Node
```
 git clone  https://github.com/inery-blockchain/inery-node
```
Export bin path

After download is finished, go to inery.node directory
```
 cd inery-node
```
Inside inery-node there is inery and inery.setup directories inery directory contains all binaries in order for blockchain protocol to work, those binaries path must be exported to OS enviroment
```
 ls    
inery inery.setup
```
Go to inery.setup directory
```
 cd inery.setup
```
Inside inery.setup there is ine.py and tools directory

Give ine.py script permission for execution with "chmod" command:
```
chmod +x ine.py
```
To export path to local os envirment for inery binaries, inside inery.setup run ine.py script with --export options
```
./ine.py --export
```
Script has written path to .bashrc file, now in order to work you paste this line in terminal, it will refresh envirmental path variable for current terminall session
```
cd; source .bashrc; cd -
```
