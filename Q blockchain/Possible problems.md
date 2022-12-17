### 1. This is a normal kind of log, your validator is on standby
![image](https://user-images.githubusercontent.com/57448493/208249008-b7518948-8f3f-493e-a67e-7eb52c6003c5.png)

### 2. Wait for synchronization
![image](https://user-images.githubusercontent.com/57448493/208249163-cfb23ea6-c389-4970-b732-c2c18b0f8a1b.png)

### 3. Restart 
![image](https://user-images.githubusercontent.com/57448493/208249198-cc12caa0-7d03-4bb1-9acc-5bf8162a6b96.png)

```
docker-compose down
docker-compose up -d
docker-compose logs -f --tail "100"
```

### 4. Add peers 
![image](https://user-images.githubusercontent.com/57448493/208249259-348490cb-3857-4940-a78c-da437146752d.png)
```Shell
cd /root/testnet-public-tools/testnet-validator/
nano .env
#Find BOOTNODE4_ADDR in discord and add them to docker-compose.yaml
```

#### 5. You may need to check your password in the `pwd.txt` you created
![image](https://user-images.githubusercontent.com/57448493/208249381-9de69420-4372-4afe-8f7f-0ee5722125ea.png)

#### 6. Delegetion validators
![image](https://user-images.githubusercontent.com/57448493/208249422-052b4da6-e25a-443d-8bbd-707e329524f3.png)
```
This does not really fit the error message, but your RN Proposal has not been executed yet - thus you are not a root node yet and cannot stake to the panel. 
Try executing your proposal (https://hq.q.org/q-governance/proposal/rootNodesMembershipVoting/5) and then staking on the root node
```


#### 7. Restart
![image](https://user-images.githubusercontent.com/57448493/208249472-060cb93d-f82f-4832-9993-4f8d8243a3c7.png)
```
docker compose stop && docker compose up -d
```


#### 8. Your config is wrong. Make sure you have keystore file and password file inside the correct folder. Please follow the setup guide step by step and make no mistakes
![image](https://user-images.githubusercontent.com/57448493/208249553-29612f43-174b-4f67-b5df-9438365221e6.png)
