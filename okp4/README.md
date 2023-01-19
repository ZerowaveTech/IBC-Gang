# OKP4 (snapshot)

Snapshot automatically updated everyday.

```
sudo systemctl stop okp4d
cp $HOME/.okp4d/data/priv_validator_state.json $HOME/.okp4d/priv_validator_state.json.backup
rm -rf $HOME/.okp4d/data
curl -L http://snapshots.cagie.tech/okp4-nemeton-1/snap_okp4.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.okp4d
mv $HOME/.okp4d/priv_validator_state.json.backup $HOME/.okp4d/data/priv_validator_state.json
sudo systemctl restart okp4d && journalctl -u okp4d -f -o cat
