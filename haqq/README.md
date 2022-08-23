# haqq
## Preparing the server

    sudo apt update && sudo apt upgrade
    sudo apt install curl tar wget clang pkg-config libssl-dev libleveldb-dev jq build-essential bsdmainutils git make ncdu htop screen unzip bc fail2ban htop -y

## Install GO 18.3 (one command) 
```
ver="1.18.1" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

## Install Haqq
```console 
NODENAME=<nodename>

echo "export NODENAME=$NODENAME" >> $HOME/.bash_profile
echo "export HAQQ_CHAIN_ID=haqq_53211-1" >> $HOME/.bash_profile
source $HOME/.bash_profile

git clone https://github.com/haqq-network/haqq
cd haqq
git checkout v1.0.3
make install

haqqd config chain-id $HAQQ_CHAIN_ID
haqqd config keyring-backend test

haqqd init $NODENAME --chain-id $HAQQ_CHAIN_ID

wget -qO $HOME/.haqqd/config/genesis.json "https://storage.googleapis.com/haqq-testedge-snapshots/genesis.json"

indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.haqqd/config/config.toml

pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.haqqd/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.haqqd/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.haqqd/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.haqqd/config/app.toml

sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0aISLM\"/" $HOME/.haqqd/config/app.toml

sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.haqqd/config/config.toml

haqqd tendermint unsafe-reset-all --home $HOME/.haqqd

sudo tee /etc/systemd/system/haqqd.service > /dev/null <<EOF
[Unit]
Description=haqq
After=network-online.target

[Service]
User=$USER
ExecStart=$(which haqqd) start --home $HOME/.haqqd
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable haqqd
sudo systemctl restart haqqd && sudo journalctl -u haqqd -f -o cat

```

## Add wallet
Create net wallet
```console
haqqd keys add <wallet>
```
or restore wallet
```console
haqqd keys add <wallet> --recover
```
## Faucet
You can request ISLM via faucet https://testedge.haqq.network/

## Create validator
```
haqqd tx staking create-validator \
  --amount 1000000000000000000aISLM \
  --from <wallet> \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(haqqd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $HAQQ_CHAIN_ID
```
