# Aura State Sync

## Info
#### Public RPC endpoint: https://aura-testnet.rpc.l0vd.com
#### Public API: https://aura-testnet.api.l0vd.com

## Guide to sync your node using State Sync:

### Copy the entire command
```
sudo systemctl stop aurad
SNAP_RPC="https://aura-testnet.rpc.l0vd.com"; \
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash); \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.aura/config/config.toml

peers="d29fa08afecaab4689147853db07dff8bbc61f18@95.216.2.219:25656/" \
&& sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.aura/config/config.toml 

aurad tendermint unsafe-reset-all --home ~/.aurad --keep-addr-book && sudo systemctl restart aurad && \
journalctl -u aurad -f --output cat
```

### Turn off State Sync Mode after synchronization
```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1false|" $HOME/.aura/config/config.toml
```
