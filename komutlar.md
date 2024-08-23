# Komutlar

### Logları Kontrol Etme
```
journalctl -fu 0gchaind -o cat
```

### Node'u Başlatma
```
systemctl start 0gchaind
```

### Node'u Durdurma
```
systemctl stop 0gchaind
```

### Node'u Yeniden Başlatma
```
systemctl restart 0gchaind
```

### Node Senkronizasyon Durumu
```
0gchaind status 2>&1 | jq .SyncInfo
```

### Node Bilgileri
```
0gchaind status 2>&1 | jq .NodeInfo
```

### Node ID'sini Öğrenme
```
0gchaind tendermint show-node-id
```

### Node IP Adresini Öğrenme
```
curl icanhazip.com
```

### Peer Bilgisi
```
echo $(0gchaind tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.0gchain/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

## Cüzdan Yönetimi

### Cüzdanların Listesini Görüntüleme
```
0gchaind keys list
```

### Cüzdan Adresini Görüntüleme
```
0gchaind keys show $OG_WALLET --bech val -a
```

### Cüzdanı İçeri Aktarma
```
0gchaind keys add $OG_WALLET --recover
```

### Cüzdanı Silme
```
0gchaind keys delete $OG_WALLET
```

### Cüzdan Bakiyesini Kontrol Etme
```
0gchaind query bank balances $OG_WALLET_ADDRESS
```

## Tokenlar

### Bir Cüzdandan Diğerine Transfer Yapma
```
0gchaind tx bank send $OG_WALLET_ADDRESS SENDING_CUZDAN_ADRESI 100000000aevmos
```

### Teklif Oylamasına Katılma
```
0gchaind tx gov vote 1 yes --from $OG_WALLET --chain-id=$OG_CHAIN_ID --gas-prices 0.00001aevmos --gas-adjustment 1.5 --gas auto -y
```

### Validatore Staking / Delegation Yapma
```
0gchaind tx staking delegate $OG_VALOPER_ADDRESS 100000000aevmos --from=$OG_WALLET --chain-id=$OG_CHAIN_ID --gas-prices 0.00001aevmos --gas-adjustment 1.5 --gas auto -y
```
### Unbonding
```
0gchaind tx staking unbond $(0gchaind keys show $OG_WALLET --bech val -a) 1000000aevmos --from $OG_WALLET --chain-id indigo-1 --fees 3000aevmos -y
```

### Staking / Mevcut Validatordan Diğerine Redelegate Yapma
`srcValidatorAddress`: Mevcut stake edilen validatör adresi
`destValidatorAddress`: Yeni stake edilecek validatör adresi
```
0gchaind tx staking redelegate srcValidatorAddress destValidatorAddress 100000000aevmos --from=$OG_WALLET --chain-id=$OG_CHAIN_ID --gas-prices 0.00001aevmos --gas-adjustment 1.5 --gas auto -y
```

### Ödülleri Çekme
```
0gchaind tx distribution withdraw-all-rewards --from=$OG_WALLET --chain-id=$OG_CHAIN_ID --gas-prices 0.00001aevmos --gas-adjustment 1.5 --gas auto -y
```

### Komisyon Ödüllerini Çekme

```
0gchaind tx distribution withdraw-rewards $OG_VALOPER_ADDRESS --from=$OG_WALLET --commission --chain-id=$OG_CHAIN_ID --gas-prices 0.00001aevmos --gas-adjustment 1.5 --gas auto -y
```

## Validator İşlemleri

### Validator Bilgileri
```
0gchaind status 2>&1 | jq .ValidatorInfo
```

### Validator İsmini Değiştirme
'NEW-NODE-NAME' yazan yere yeni validator/moniker adınızı yazın. TR karakter içermemelidir.
```
0gchaind tx staking edit-validator \
--new-moniker=NEW-NODE-NAME \
--chain-id=$OG_CHAIN_ID \
--from=$OG_WALLET \
--gas-prices 0.00001aevmos \
--gas-adjustment 1.5 \
--gas auto -y
```

### Validator Komisyon Oranını Değiştirme
'commission-rate' yazan bölümdeki değeri değiştiriyoruz.
```
0gchaind tx staking edit-validator --commission-rate "0.02" --moniker=$OG_NODENAME --from $OG_WALLET --chain-id $OG_CHAIN_ID --gas-prices 0.00001aevmos --gas-adjustment 1.5 --gas auto - y
```

### Validator Bilgilerinizi Düzenleme
Bu bilgileri değiştirmeden önce, https://keybase.io/ adresine kaydolmalı ve aşağıdaki kodda görüldüğü gibi 16 haneli kodunuzu (XXXX0000XXXX0000) almalısınız. Ayrıca profil resmi vb. ayarları da yapabilirsiniz.
`$OG_NODENAME` ve `$OG_WALLET`: Validator (Moniker) ve cüzdan isminiz, bunları değiştirmenize gerek yok. Çünkü bunları değişkenlere ekledik.
```
0gchaind tx staking edit-validator \
--moniker=$OG_NODENAME \
--identity=XXXX0000XXXX0000\
--website="VARSA KENDİ WEBSİTENİZİ YAZABİLİRSİNİZ" \
--details="BU BÖLÜME KENDİNİZİ TANITAN BİR CÜMLE YAZABİLİRSİNİZ" \
--chain-id=$OG_CHAIN_ID \
--from=$OG_WALLET
```

### Validator Detayları
```
0gchaind q staking validator $(0gchaind keys show $OG_WALLET --bech val -a)
```

### Jailing Bilgisi
```
0gchaind q slashing signing-info $(0gchaind tendermint show-validator)
```

### Slashing Parametreleri
```
0gchaind q slashing params
```

### Validator'u Jail'den Kurtarma
```
0gchaind tx slashing unjail --from $OG_WALLET --chain-id $OG_CHAIN_ID --gas-prices 0.00001aevmos --gas-adjustment 1.5 --gas auto -y
```

### Aktif Validatorlar Listesi
```
0gchaind q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl
```

### Validator Anahtarını Kontrol Etme
```
[[ $(0gchaind q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(0gchaind status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Anahtar durumunuz iyi" || echo -e "Anahtar durumunuz hatalı"
```

### Signing Bilgisi
```
0gchaind q slashing signing-info $(0gchaind tendermint show-validator)
```
