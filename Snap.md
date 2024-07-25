```
sudo systemctl stop selfchaind

mv $HOME/.selfchain/data/priv_validator_state.json $HOME/.selfchain/priv_validator_state.json.backup 

selfchaind tendermint unsafe-reset-all --home $HOME/.selfchain --keep-addr-book 
curl http://37.120.189.81/selfchain_mainnet/selfchain_snap.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME

cp $HOME/.selfchain/priv_validator_state.json.backup $HOME/.selfchain/data/priv_validator_state.json 

sudo systemctl restart selfchaind
sudo journalctl -u selfchaind -f --no-hostname -o cat
```
