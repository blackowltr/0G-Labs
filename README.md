# 0G-Labs

![0gtasarimim)](https://github.com/blackowltr/blackowltr.github.io/assets/107190154/48243956-1bd1-431d-aba2-a9463825342c)

## Linkler
 ✔️ [Website](https://0g.ai/) |
 ✔️ [Blockchain Explorer](https://testnet.0g.explorers.guru/) |
 ✔️ [Docs](https://docs.0g.ai/0g-doc) |
 ✔️ [GitHub](https://github.com/0glabs) |
 ✔️ [Discord](https://discord.gg/0glabs)

---

## **Sistem Gereksinimleri**

| Bileşenler  | Minimum      | **Önerilen**  |
|-------------|--------------|---------------|
| CPU         | 4            | 8             |
| RAM         | 8 GB         | 16 GB         |
| Depolama    | 500 GB SSD   | 1 TB SSD      |

---

## **Network Bilgileri**

- **Network Chain ID:** zgtendermint_16600-2
- **Binary:** 0g-chain
- **Denom:** ua0gi
- **Çalışma dizini:** .0gchain

---

## **Halka Açık Servisler**

- **RPC:** https://rpc.nodejumper.io/0gtestnet
- **API:** https://rest.nodejumper.io/0gtestnet
- **Explorer:** [https://explorer.blackowl.tech/0G](https://testnet.0g.explorers.guru/)

---

## **Peer ve Addrbook**

### Peer
Hızlı bağlantılar veya state-sync için bir peer kullanabilirsiniz:

```shell
PEERS="b2cc2277199fe563701d601191f7a9bdb2b012a4@149.50.119.154:12656,625e4d70d537e67e10ef7ad9b1383d99ce91d4d9@159.69.61.114:47656,0a63668e51287b4bf10085a2c2f8c051da80ec67@149.50.125.67:12656,2de1b95c9c905ed5ae323248df4da45ed9e3d779@149.50.107.182:12656,cc28265c1789db651ae4963a730d18827d500ed3@149.50.124.199:12656,dae234c0a999f271da30b0b13eb4c51d1536d281@149.50.114.187:12656,cb27bb0248a692d78be1e65cc84ccda827a15ec6@149.50.114.15:12656,2050f8e1395a9d3baee7aa9f2bd5fa9d6c123e81@149.50.125.24:12656,c9f121f4ad54b7299e622335d54932ca0c481700@185.215.164.12:26656,b834a5df869e37243832b0edc3008ee6ed5bc710@149.50.119.174:12656,9dcb40c51fae47292418a9fe085fcc94d4e9dc15@148.113.9.176:36656,80cdd1a027020305972265dd6eb0e757950cfd2e@94.130.132.219:26656,31dd96cf2cc0bca78e52985f3871566020f5260c@136.243.66.243:26656,9c1d43f9998b849b8376d403f2f77217753debc6@217.15.167.74:26656,890a8fdf7ae003e381d4123c5fa70d6aa1125391@176.9.104.216:26656,9bc3ffcec4554f291fe4630f356e477ddd6fea97@149.50.111.45:12656,b25020773d3ccc0c7d7c3ead4bfb1758bd678210@185.218.125.42:26656,a43efb5f3311d24bf2ddb1dccea0b8e6c57516b3@149.50.119.207:12656,15f982a56340186a795b2aef981e6197530f6145@38.242.131.32:26656,7345dc517473fe18fe0071e74625f696ca194491@65.108.125.165:12656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.0gchain/config/config.toml

sudo systemctl restart 0gchaind
sudo journalctl -u 0gchaind -f --no-hostname -o cat
```

### Addrbook
Addrbook'u hızlı başlatma için kullanabilirsiniz:

```shell
curl -s https://snapshots-testnet.nodejumper.io/0g-testnet/addrbook.json > $HOME/.0gchain/config/addrbook.json

sudo systemctl restart 0gchaind
sudo journalctl -u 0gchaind -f --no-hostname -o cat
```
