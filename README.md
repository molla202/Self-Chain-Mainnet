<h1 align="center"> Self Chain Mainnet </h1>




 * [Topluluk kanalımız](https://t.me/corenodechat)<br>
 * [Topluluk Twitter](https://twitter.com/corenodeHQ)<br>
 * [Warden Website](https://wardenprotocol.org/)<br>
 * [Blockchain Explorer](https://explorer.corenodehq.com/Warden%20Testnet)<br>
 * [Discord](https://discord.gg/7rzkxXRK)<br>
 * [Twitter](https://twitter.com/wardenprotocol)<br>

## 💻 Sistem Gereksinimleri
| Bileşenler | Minimum Gereksinimler | 
| ------------ | ------------ |
| CPU |	4 or 8 |
| RAM	| 8+ or 16+ GB |
| Storage	| 400 GB SSD |

### 🚧Gerekli kurulumlar
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

### 🚧 Go kurulumu
```
cd $HOME
VER="1.21.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

### 🚧 Dosyaları çekelim ve kuralım

```
cd $HOME
wget https://snap.nodex.one/selfchain/selfchaind
chmod +x selfchaind
```
```
mkdir -p $HOME/.selfchain/cosmovisor/genesis/bin
mv selfchaind $HOME/.selfchain/cosmovisor/genesis/bin/
```
```
sudo ln -s $HOME/.selfchain/cosmovisor/genesis $HOME/.selfchain/cosmovisor/current -f
sudo ln -s $HOME/.selfchain/cosmovisor/current/bin/selfchaind /usr/local/bin/selfchaind -f
```
```
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0
```
### 🚧 Servis oluşturalım
```
sudo tee /etc/systemd/system/selfchaind.service > /dev/null << EOF
[Unit]
Description=selfchain node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.selfchain"
Environment="DAEMON_NAME=selfchaind"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.selfchain/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
```
```
sudo systemctl daemon-reload
sudo systemctl enable selfchaind
```
### 🚧 İnit
```
selfchaind config chain-id self-1
selfchaind config keyring-backend file
selfchaind config node tcp://localhost:12857
```
* Moniker adını yaz
```
selfchaind init yaz-bura --chain-id self-1
```
### 🚧 Genesis addrbook
```
curl -Ls https://raw.githubusercontent.com/molla202/Self-Chain-Mainnet/main/genesis.json > $HOME/.selfchain/config/genesis.json
```
### 🚧 Gas ayarı
```
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025uward\"/;" ~/.selfchain/config/app.toml
```
### 🚧 Peer
```
SEEDS="5f5cfac5c38506fbb4275c19e87c4107ec48808d@seeds.nodex.one:12810,b307b56b94bd3a02fcad5b6904464a391e13cf48@128.199.33.181:26656,71b8d630e7c3e31f2743fda68e6d3ac64f41cece@209.97.174.97:26656,6ae10267d8581414b37553655be22297b2f92087@174.138.25.159:26656,9512a59cf93b987aff830148421a514cacb8a1b8@170.64.141.15:26656,55d3e1761d6752eeb72b8b86decbca0d56f6a885@159.89.173.150:26656,2f547f93392d7351c74a0d8cae1d44f172cf32e5@64.227.156.23:26656"
PEERS="b307b56b94bd3a02fcad5b6904464a391e13cf48@128.199.33.181:26656,71b8d630e7c3e31f2743fda68e6d3ac64f41cece@209.97.174.97:26656,6ae10267d8581414b37553655be22297b2f92087@174.138.25.159:26656,9512a59cf93b987aff830148421a514cacb8a1b8@170.64.141.15:26656,55d3e1761d6752eeb72b8b86decbca0d56f6a885@159.89.173.150:26656,2f547f93392d7351c74a0d8cae1d44f172cf32e5@64.227.156.23:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.selfchain/config/config.toml
```
### config pruning
```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.selfchain/config/app.toml
```
### 🚧 Snap
```
selfchaind tendermint unsafe-reset-all --home $HOME/.selfchain
if curl -s --head curl http://37.120.189.81/selfchain_testnet/selfchain_snap.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl http://37.120.189.81/selfchain_testnet/selfchain_snap.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.selfchain
    else
  echo no have snap
fi
```

### 🚧 Port ayarı
```
CUSTOM_PORT=115

sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.selfchain/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"localhost:9090\"%address = \"localhost:${CUSTOM_PORT}90\"%; s%^address = \"localhost:9091\"%address = \"localhost:${CUSTOM_PORT}91\"%" $HOME/.selfchain/config/app.toml
```
### 🚧 Başlatalım
Not: önce ufak bir peer ayarı gerekli
```
nano $HOME/.selfchain/config/config.toml
```
`handshake_timeout = "120s"`
`dial_timeout = "120s"`
```
sudo systemctl restart selfchaind
journalctl -fu selfchaind -o cat
```


### 🚧 Cüzdan olusturalım
```
selfchaind keys add cüzdan-adi
```
### 🚧 Validator Olusturma



### Komple Silme
```
sudo systemctl stop selfchaind
sudo systemctl disable selfchaind
sudo rm -rf /etc/systemd/system/selfchaind.service
sudo rm $(which selfchaind)
sudo rm -rf $HOME/.selfchain
sed -i "/SELFCHAIN_/d" $HOME/.bash_profile
```

