#### delegation to the validator
```
noisd tx staking delegate <valoper_address> 1000000unois --from <name_wallet> --fees 5000unois -y
```
#### collect the rewards
```
noisd tx distribution withdraw-rewards <valoper_address> --from <name_wallet> --fees 5000unois --commission -y

noisd tx distribution withdraw-all-rewards --from <name_wallet> --fees 5000unois -y
```
#### delegating to another validator
```
haqqd tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000aISLM --from <name_wallet> --fees 500aISLM -y
```
#### validator decoration
```
haqqd tx staking edit-validator \
--identity 3162A31DFBF2AD6B \
--details "напишите что нибудь о себе" \
--website "можно вставить ссылку на твиттер" \
--chain-id haqq_54211-2 \
--from <имя кошелька> \
--yes
```
#### voting
```
haqqd tx gov vote 151 yes --from <name_wallet> --fees 555aISLM
```
