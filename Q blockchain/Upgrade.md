```Shell
screen -S -X q quit
```

```Shell
cd /root/testnet-public-tools/testnet-validator/
nano .env
```

Change the docker image directly in your .env file:
```
QCLIENT_IMAGE=qblockchain/q-client:1.2.2
```
#### Starting a new terminal window
```Shell
screen -S q
```

Pull (and overwrite) the latest docker image
```Shell
$ docker-compose pull
```

Restart with new configs & images
```Shell
$ docker-compose up -d
```

```Shell
docker-compose logs -f --tail 100
```
```Shell
#Exit the created window `q`
ctrl A+D
```
Now your validator node should restart and synchronise with the testnet again.
