#### 1.Update - at a height of 800
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

