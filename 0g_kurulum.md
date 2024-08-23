# 💾 0G Node Kurulum Kılavuzu

## **Sistem Gereksinimleri**

| **Bileşen**   | **Minimum Gereksinimler** | **Önerilen Gereksinimler** |
|---------------|---------------------------|----------------------------|
| **CPU**       | 4 Çekirdek                 | 8 Çekirdek                  |
| **RAM**       | 8 GB                       | 16 GB                       |
| **Depolama**  | 500 GB SSD                 | 1 TB SSD                    |

## 1. Sistem Paketlerini Güncelle ve Yükselt

Başlamadan önce, sisteminizin en son güncellemeleri aldığından emin olun:

```shell
sudo apt update && sudo apt upgrade -y
```

Bu komut, paket listelerinizi günceller ve güvenlik güncellemeleri dahil olmak üzere paketlerinizin en yeni sürümlerini yükler.

## 2. Gerekli Paketleri Yükle

Kurulum için gerekli paketleri yükleyin:

```shell
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

Bu paketler, yazılım indirme, derleme, süreç yönetimi ve daha fazlası için araçlar sağlar.

## 3. Go'yu Yükle (Eğer Daha Önce Yüklenmediyse)

### 3.1. Go'yu İndir

Sistem mimariniz için Go binary dosyasını indirin:

```shell
cd $HOME
VER="1.21.3"
wget https://golang.org/dl/go$VER.linux-amd64.tar.gz
```

### 3.2. Go'yu Kur

İndirilen arşivi çıkartın ve Go'yu ayarlayın:

```shell
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf go$VER.linux-amd64.tar.gz
rm go$VER.linux-amd64.tar.gz
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source ~/.bash_profile
```

### 3.3. Go Binaryleri için Bir Dizin Oluşturun

```shell
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

Bu dizin, özel Go binary dosyalarınızı saklayacaktır.

## 4. Ortam Değişkenlerini Ayarla

```shell
export WALLET="WALLETNAME"
export MONIKER="NODENAME"
export OG_CHAIN_ID="zgtendermint_16600-2"
export OG_PORT="26"
```

Bu değişkenleri shell profilinize yükleyin:

```shell
source ~/.0gchain.env
```

## 5. 0G Chain Binary Dosyasını İndir ve Derle

### 5.1. 0G Chain Reposunu Klonla

```shell
cd && rm -rf 0g-chain
git clone https://github.com/0glabs/0g-chain
cd 0g-chain
git checkout v0.3.1
```

### 5.2. Binary Dosyasını Derle

0G chain binary dosyasını kaynak koddan derleyin.

## 6. 0G Chain Node'u Yapılandır

### 6.1. Node'u Başlat

Chain ID ve moniker ile node'u başlatın:

```shell
0gchaind config node tcp://localhost:${OG_PORT}657
0gchaind config keyring-backend os
0gchaind config chain-id $OG_CHAIN_ID
0gchaind init "$MONIKER" --chain-id $OG_CHAIN_ID
```

### 6.2. Genesis Dosyasını ve Addrbook İndir

Testnet için genesis dosyasını indirin:

```shell
rm -f $HOME/.0gchain/config/genesis.json && curl -L https://snapshots-testnet.nodejumper.io/0g-testnet/genesis.json -o $HOME/.0gchain/config/genesis.json
curl -L https://snapshots-testnet.nodejumper.io/0g-testnet/addrbook.json > $HOME/.0gchain/config/addrbook.json
```

### 6.3. Seeds ve Peers Ayarlarını Yapılandır

Node'un ağa bağlanması için yapılandırmayı yapın:

```shell
SEEDS="81987895a11f6689ada254c6b57932ab7ed909b6@54.241.167.190:26656,010fb4de28667725a4fef26cdc7f9452cc34b16d@54.176.175.48:26656,e9b4bc203197b62cc7e6a80a64742e752f4210d5@54.193.250.204:26656,68b9145889e7576b652ca68d985826abd46ad660@18.166.164.232:26656"
PEERS="b2cc2277199fe563701d601191f7a9bdb2b012a4@149.50.119.154:12656,625e4d70d537e67e10ef7ad9b1383d99ce91d4d9@159.69.61.114:47656,0a63668e51287b4bf10085a2c2f8c051da80ec67@149.50.125.67:12656,2de1b95c9c905ed5ae323248df4da45ed9e3d779@149.50.107.182:12656,cc28265c1789db651ae4963a730d18827d500ed3@149.50.124.199:12656,dae234c0a999f271da30b0b13eb4c51d1536d281@149.50.114.187:12656,ba38d90d6a9f0b901ca54f9d44ccbe7e3ec59325@149.50.109.145:12656,2050f8e1395a9d3baee7aa9f2bd5fa9d6c123e81@149.50.125.24:12656,c9f121f4ad54b7299e622335d54932ca0c481700@185.215.164.12:26656,b834a5df869e37243832b0edc3008ee6ed5bc710@149.50.119.174:12656,9dcb40c51fae47292418a9fe085fcc94d4e9dc15@148.113.9.176:36656,80cdd1a027020305972265dd6eb0e757950cfd2e@94.130.132.219:26656,31dd96cf2cc0bca78e52985f3871566020f5260c@136.243.66.243:26656,9c1d43f9998b849b8376d403f2f77217753debc6@217.15.167.74:26656,890a8fdf7ae003e381d4123c5fa70d6aa1125391@176.9.104.216:26656,9bc3ffcec4554f291fe4630f356e477ddd6fea97@149.50.111.45:12656,b25020773d3ccc0c7d7c3ead4bfb1758bd678210@185.218.125.42:26656,a43efb5f3311d24bf2ddb1dccea0b8e6c57516b3@149.50.119.207:12656,15f982a56340186a795b2aef981e6197530f6145@38.242.131.32:26656,5037b5d7b52c28e5c12703d638a3afba2f85c1d4@149.50.119.24:12656"
sed -i -e "s|^seeds *=.*|seeds = \"$SEEDS\"|" $HOME/.0gchain/config/config.toml
sed -i -e "s|^persistent_peers *=.*|persistent_peers = \"$PEERS\"|" $HOME/.0gchain/config/config.toml
```

## 7. Portları Ayarla ve Prometheus'u Etkinleştir

### 7.1. Portları Değiştir

Varsayılan portları özel portlarla değiştirin:

```shell
sed -i.bak -e "s%:1317%:${OG_PORT}317%g; s%:8080%:${OG_PORT}080%g; s%:9090%:${OG_PORT}090%g; s%:9091%:${OG_PORT}091%g; s%:8545%:${OG_PORT}545%g; s%:8546%:${

OG_PORT}546%g; s%:6065%:${OG_PORT}065%g" $HOME/.0gchain/config/app.toml
```

### 7.2. Prometheus'u Etkinleştir ve Indexing'i Devre Dışı Bırak

Prometheus metriklerini etkinleştirin ve indexing'i devre dışı bırakın:

```shell
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.0gchain/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.0gchain/config/config.toml
```

## 8. Pruning Yapılandırmasını Yap

Disk kullanımını yönetmek için pruning parametrelerini ayarlayın:

```shell
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.0gchain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.0gchain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.0gchain/config/app.toml
```

## 9. Minimum Gas Price Ayarla

İşlem hatalarını önlemek için minimum gas price'ı ayarlayın:

```shell
sed -i "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0ua0gi\"/" $HOME/.0gchain/config/app.toml
```

## 10. Systemd Service Dosyası Oluştur

0G chain node sürecini yönetmek için bir systemd service dosyası (`0gchaind.service`) oluşturun:

```shell
sudo tee /etc/systemd/system/0gchaind.service > /dev/null <<EOF
[Unit]
Description=Og Chain Service
After=network-online.target

[Service]
User=$USER
WorkingDirectory=$HOME/.0gchain
ExecStart=$(which 0gchaind) start --home $HOME/.0gchain
Restart=on-failure
RestartSec=5
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

## 11. Reset, Snapshot İndir ve Node'u Başlat

### 11.1. Node Verilerini Resetle

Node verilerini resetleyin:

```shell
0gchaind tendermint unsafe-reset-all --home $HOME/.0gchain
```

### 11.2. Snapshot İndir (Opsiyonel)

Daha hızlı node başlatma için bir snapshot mevcutsa kontrol edin. Snapshotları [NodeJumper](https://app.nodejumper.io/0g-testnet/sync) adresinde bulabilirsiniz.

### 11.3. Node'u Başlat

Servis dosyasını etkinleştir ve başlat:

```shell
sudo systemctl daemon-reload
sudo systemctl enable 0gchaind
sudo systemctl restart 0gchaind
```

### 11.4. Logları İzle

Hatalar veya uyarılar için node loglarını izleyin:

```shell
sudo journalctl -u 0gchaind -f
```

Artık 0G node'unuz çalışıyor ve testnet'e bağlı durumda olmalı.
