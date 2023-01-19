# Planq

## Snapshot
Snapshot automatically updated everyday.

```
sudo systemctl stop planqd
cp $HOME/.planqd/data/priv_validator_state.json $HOME/.planqd/priv_validator_state.json.backup
rm -rf $HOME/.planqd/data
curl -L https://planq-snapshots.cagie.tech/snap_planq.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.planqd
mv $HOME/.planqd/priv_validator_state.json.backup $HOME/.planqd/data/priv_validator_state.json
sudo systemctl restart planqd && journalctl -u planqd -f -o cat

## Statesync

STATE_SYNC_RPC=https://planq-rpc.cagie.tech:443
STATE_SYNC_PEER=938f1720a3ec8a168553a9d5b3be5eee1d078108@planq-rpc.cagie.tech:14656
LATEST_HEIGHT=$(curl -s $STATE_SYNC_RPC/block | jq -r .result.block.header.height)
SYNC_BLOCK_HEIGHT=$(($LATEST_HEIGHT - 1000))
SYNC_BLOCK_HASH=$(curl -s "$STATE_SYNC_RPC/block?height=$SYNC_BLOCK_HEIGHT" | jq -r .result.block_id.hash)

sed -i \
  -e "s|^enable *=.*|enable = true|" \
  -e "s|^rpc_servers *=.*|rpc_servers = \"$STATE_SYNC_RPC,$STATE_SYNC_RPC\"|" \
  -e "s|^trust_height *=.*|trust_height = $SYNC_BLOCK_HEIGHT|" \
  -e "s|^trust_hash *=.*|trust_hash = \"$SYNC_BLOCK_HASH\"|" \
  -e "s|^persistent_peers *=.*|persistent_peers = \"$STATE_SYNC_PEER\"|" \
  $HOME/.planqd/config/config.toml

sudo systemctl stop planqd
planqd tendermint unsafe-reset-all --home $HOME/.planqd && \
sudo systemctl restart planqd && journalctl -fu planqd -o cat
