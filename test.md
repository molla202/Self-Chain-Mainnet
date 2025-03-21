<h1 align="center"> Self Chain Test V2 </h1>




 * [Topluluk kanalımız](https://t.me/corenodechat)<br>
 * [Topluluk Twitter](https://twitter.com/corenodeHQ)<br>

## 💻 Sistem Gereksinimleri
| Bileşenler | Minimum Gereksinimler | 
| ------------ | ------------ |
| CPU |	4 or 8 |
| RAM	| 8+ or 16+ GB |
| Storage	| 400 GB SSD |

### Explorer

https://testnet.selfchain.xyz/self/staking

https://discord.gg/64eUrMcp

### Public RPC and Explorer

SOON...

### 🚧Gerekli kurulumlar
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip unrar -y
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
wget https://github.com/hotcrosscom/Self-Chain-Releases/releases/download/mainnet-v1.0.1/selfchaind-linux-amd64
mv selfchaind-linux-amd64 selfchaind
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
cd $HOME
wget https://github.com/molla202/Self-Chain-Mainnet/raw/main/selfchaind.rar
unrar x selfchaind.rar
chmod +x selfchaind
mkdir -p $HOME/.selfchain/cosmovisor/upgrades/v2/bin
mv selfchaind $HOME/.selfchain/cosmovisor/upgrades/v2/bin/
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
selfchaind config chain-id selfchain-testnet
selfchaind config keyring-backend file
selfchaind config node tcp://localhost:11557
```
* Moniker adını yaz
```
selfchaind init yaz-bura --chain-id selfchain-testnet
```
### 🚧 Genesis addrbook
```
curl -Ls https://raw.githubusercontent.com/molla202/Self-Chain-Mainnet/refs/heads/main/geneisitest.json > $HOME/.selfchain/config/genesis.json
```
### 🚧 Gas ayarı
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.005uslf\"|" $HOME/.selfchain/config/app.toml
```
### 🚧 Peer
```
PEERS="c2290f5420549b13ce2fdcbdc5e4a42925b9e254@3.144.209.161:26656,3c5b1b57a5aaea305eb900d7147f6b89ff46c74f@65.109.113.242:12656,a0614ac32d5eefe6aa79cdf5c7a220b749abcbd9@184.107.182.148:60000,52cab9b5fa60be3fbe682ce307bee313e9508c5e@3.144.12.35:26656,c0dbddb16c0060c99f243b80b20c9bccce71a7bf@3.21.176.122:26656,3a6608f456836bdd69d65ae2cf5854f8cd76c27b@3.147.222.52:26656,5157d0c772be8c2bda55714e2aa809c4470e7e7f@27.79.159.17:26656,be4ec3347a5f30f16f3a39b689c2ee3b1799c2b1@118.68.212.23:26656,88bd951b765bb2751f8b45c1c0d067c11b123d4c@135.181.181.59:11556,f8ac3f2b6b07bf42790b7a0c553715ebdd7bfee6@152.53.108.188:23356,06d505c499b2c99b656320b954944038794b223b@116.202.218.189:11456,e8aee78359d5a118ee638c9443d7d317096290d1@62.164.219.0:27656,b0e373db9a357288ccee332d204316334d1a6e09@75.119.131.249:26656,f6c8f342c44956f703754c90aca76f542ee775a5@156.67.83.138:27656,9d33dd323bb02e1dda974f6400e809fa2b3740e9@152.53.125.167:27656,89ac90fe63fac4e388459e49ef73cc6df3ca9a82@110.138.86.41:26603,5e318abe5753a9abad273e10d3a8029869c3fbf6@65.21.141.250:26656,c6835dc6ba0b1fcc86602373fddb451fdca8890b@5.9.87.231:60756,54bc6768af48108d7c50344362165c7870d89275@37.27.52.25:13656,406740ae6e321cb9d42397a609c9b597248330a1@144.76.97.251:41656,7923e9a64c2b2986c54035aaace4db2dfc45ff21@167.235.102.45:11456,1a4b18522841d365afb047f10582d3528605f0a7@95.214.55.21:11556,5f399d99995d007f3154aed796f00ddd363b915b@158.220.88.149:11556,5c3da4390d658c9b448398b81f63fb7ec6d1cf81@149.86.227.209:11556,7fafa10576644047f08279e3bfd88fd990a82c1e@65.21.221.110:11556,727adb73e118a03dafdbe009c23fd7ce84264597@38.242.236.165:26656,1f4c26f65ee36c2ef8eb98c2d37fa6374c169e60@75.119.128.23:26656,ba7034bbb6b47912700b879737b4d4aa3c33e617@183.80.241.230:26656,6bf4e8cfdc79a3c54615f5948fba0eba5b977154@65.109.69.117:26656"
SEEDS=""
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.selfchain/config/config.toml
```
### config pruning and indexer
```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.selfchain/config/app.toml
```
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.selfchain/config/config.toml
```
### 🚧 Snap
NOT : yenilecek yapmayın.
```
mv $HOME/.selfchain/data/priv_validator_state.json $HOME/.selfchain/priv_validator_state.json.backup 

selfchaind tendermint unsafe-reset-all --home $HOME/.selfchain --keep-addr-book 
curl http://37.120.189.81/selfchain_mainnet/selfchain_snap.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME

cp $HOME/.selfchain/priv_validator_state.json.backup $HOME/.selfchain/data/priv_validator_state.json 
```

### 🚧 Port ayarı
```
CUSTOM_PORT=115

sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.selfchain/config/config.toml
sed -i -e "s%^address = \"tcp://localhost:1317\"%address = \"tcp://localhost:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"localhost:9090\"%address = \"localhost:${CUSTOM_PORT}90\"%; s%^address = \"localhost:9091\"%address = \"localhost:${CUSTOM_PORT}91\"%" $HOME/.selfchain/config/app.toml
```
### 🚧 Başlatalım
```
sudo systemctl restart selfchaind
journalctl -fu selfchaind -o cat
```


### 🚧 Cüzdan olusturalım
```
selfchaind keys add cüzdan-adi
```
### 🚧 Validator Olusturma
```
selfchaind tx staking create-validator \
    --amount=1000000uslf \
    --pubkey=$(selfchaind tendermint show-validator) \
    --moniker="moniker-adi-yaz" \
    --website="" \
    --details="" \
    --chain-id="selfchain-testnet" \
    --commission-rate="0.10" \
    --commission-max-rate="0.15" \
    --commission-max-change-rate="0.05" \
    --min-self-delegation="1" \
    --gas="auto" \
    --gas-adjustment="1.4" \
    --gas-prices="0.005uslf" \
    --from="cüzdan-adi" \
    -y
```

### Yararlı kodlar
#### Ödülleri çek 
```
selfchaind tx distribution withdraw-rewards $(selfchaind keys show wallet --bech val -a) --from wallet --commission --chain-id selfchain-testnet --gas auto --gas-adjustment 1.4 --gas-prices="0.005uslf" -y
```

#### kendine delege
```
selfchaind tx staking delegate $(selfchaind keys show wallet --bech val -a) miktar000000uslf --from wallet --chain-id selfchain-testnet --gas auto --gas-adjustment 1.4 --gas-prices="0.005uslf" -y 
```


### Komple Silme
```
sudo systemctl stop selfchaind
sudo systemctl disable selfchaind
sudo rm -rf /etc/systemd/system/selfchaind.service
sudo rm $(which selfchaind)
sudo rm -rf $HOME/.selfchain
sed -i "/SELFCHAIN_/d" $HOME/.bash_profile
```

