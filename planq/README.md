# Planq snapshot

Snapshot automatically updated everyday.

```
sudo systemctl stop planqd
cp $HOME/.planqd/data/priv_validator_state.json $HOME/.planqd/priv_validator_state.json.backup
rm -rf $HOME/.planqd/data
curl -L https://planq-snapshots.cagie.tech/snap_planq.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.planqd
mv $HOME/.planqd/priv_validator_state.json.backup $HOME/.planqd/data/priv_validator_state.json
sudo systemctl restart planqd && journalctl -u planqd -f -o cat

# Planq statesync

Snapshot automatically updated everyday.

```
sudo systemctl stop planqd
cp $HOME/.planqd/data/priv_validator_state.json $HOME/.planqd/priv_validator_state.json.backup
rm -rf $HOME/.planqd/data
curl -L https://planq-snapshots.cagie.tech/snap_planq.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.planqd
mv $HOME/.planqd/priv_validator_state.json.backup $HOME/.planqd/data/priv_validator_state.json
sudo systemctl restart planqd && journalctl -u planqd -f -o cat
