# Migrate haqq (haqq_54211-2) to Cosmovisor
If, after installing the node, you need to migrate to the space viewer, do the following.

Install Cosmovisor
```
go install github.com/cosmos/cosmos-sdk/cosmovisor/cmd/cosmovisor@v1.0.0
```
Create directory
```
mkdir -p $HOME/.haqqd/cosmovisor/genesis/bin
mkdir $HOME/.haqqd/cosmovisor/upgrades
```
Stop haqq node
```
sudo systemctl stop haqqd
```
Copy bin to cosmovisor folder
```
cp $(which haqqd) $HOME/.haqqd/cosmovisor/genesis/bin/
```
Delete serivce haqqd
```
rm -rf /etc/systemd/system/rebusd.service
```
Creating new service with cosmovisor
```
sudo tee /etc/systemd/system/haqqd.service > /dev/null <<EOF  
[Unit]
Description=cosmovisor
After=network-online.target
[Service]
User=$USER
ExecStart=$(which cosmovisor) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.haqqd"
Environment="DAEMON_NAME=haqqd"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=true"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
[Install]
WantedBy=multi-user.target
EOF
```
Start service
```
sudo systemctl daemon-reload
sudo systemctl enable haqqd
sudo systemctl restart haqqd
```
Check logs
```
journalctl -u haqqd -f -o cat
```
