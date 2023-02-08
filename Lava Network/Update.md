#### 1. Update - at a height of 800
```
systemctl stop lavad

wget https://lava-binary-upgrades.s3.amazonaws.com/testnet/v0.4.0/lavad
chmod +x lavad
mv lavad $HOME/go/bin/
systemctl restart lavad && journalctl -u lavad -f -o cat
```

#### 2. Update - at a height of 22300
```
cd $HOME
git clone https://github.com/lavanet/lava && cd lava
git checkout v0.4.3
make build
systemctl stop lavad
mv $HOME/lava/build/lavad $(which lavad)
systemctl restart lavad && journalctl -u lavad -f -o cat
```

#### 3. Update - at a height of 41735
```
cd $HOME/lava
git pull
git checkout v0.4.4
make build
systemctl restart lavad && journalctl -u lavad -f -o cat
systemctl stop lavad
mv $HOME/lava/build/lavad $(which lavad)
```

#### 4 Update - at a height of 63760
```
cd $HOME/lava
git pull
git checkout v0.5.2
make build

systemctl stop lavad
mv $HOME/lava/build/lavad $(which lavad)

systemctl restart lavad && journalctl -u lavad -f -o cat
```
