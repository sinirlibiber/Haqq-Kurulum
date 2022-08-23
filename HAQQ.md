#!/bin/bash

while true
do

# Logo

echo "=================================================="
echo "=================================================="  

# Menu

PS3='Select an action: '
options=(
"Install node"
"Check logs"
"Check balance"
"Sync via stateSync"
"Request tokens"
"Create validator"
"Exit")
select opt in "${options[@]}"
do
case $opt in

"Install node")
echo "============================================================"
echo "Your Node Name:"
echo "============================================================"
read NODENAME
echo "============================================================"
echo "Your Wallet Name:"
echo "============================================================"
read WALLETNAME
echo export NODENAME=${NODENAME} >> $HOME/.bash_profile
echo export WALLETNAME=${WALLETNAME} >> $HOME/.bash_profile
echo export CHAIN_ID="haqq_53211-1" >> $HOME/.bash_profile
source ~/.bash_profile

#UPDATE APT
sudo apt update && sudo apt upgrade -y
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y

#INSTALL GO
rm -r /usr/local/go
rm -r /usr/lib/go-1.13
wget https://golang.org/dl/go1.18.3.linux-amd64.tar.gz; \
rm -rv /usr/local/go; \
tar -C /usr/local -xzf go1.18.3.linux-amd64.tar.gz && \
rm -v go1.18.3.linux-amd64.tar.gz && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile && \
source ~/.bash_profile && \
go version

rm -rf $HOME/haqq $HOME/.haqqd
#INSTALL
cd $HOME && \
git clone -b v1.0.3 https://github.com/haqq-network/haqq && \
cd haqq && \
make install

haqqd init $NODENAME --chain-id $CHAIN_ID


echo "============================================================"
echo "Save your mnemonic !"
echo "============================================================"
#WALLET
haqqd keys add $WALLETNAME

haqqd tendermint unsafe-reset-all --home $HOME/.haqqd
rm $HOME/.haqqd/config/genesis.json
wget -O $HOME/.haqqd/config/genesis.json "https://storage.googleapis.com/haqq-testedge-snapshots/genesis.json"
wget -O $HOME/.haqqd/config/addrbook.json "https://raw.githubusercontent.com/NodesBlocks/Haqq-Network/main/addrbook.json"

SEEDS="8f7b0add0523ec3648cb48bc12ac35357b1a73ae@195.201.123.87:26656,899eb370da6930cf0bfe01478c82548bb7c71460@34.90.233.163:26656,f2a78c20d5bb567dd05d525b76324a45b5b7aa28@34.90.227.10:26656,4705cf12fb56d7f9eb7144937c9f1b1d8c7b6a4a@34.91.195.139:26656"
PEERS=""; \
sed -i.bak -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.haqqd/config/config.toml


# config pruning
indexer="null"
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"

sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.haqqd/config/config.toml
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.haqqd/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.haqqd/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.haqqd/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.haqqd/config/app.toml



tee $HOME/haqqd.service > /dev/null <<EOF
[Unit]
Description=haqq
After=network.target
[Service]
Type=simple
User=$USER
ExecStart=$(which haqqd) start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

sudo mv $HOME/haqqd.service /etc/systemd/system/

# start service
sudo systemctl daemon-reload
sudo systemctl enable haqqd
sudo systemctl restart haqqd

break
;;

"Check logs")

journalctl -u haqqd -f -o cat

break
;;


"Check balance")
haqqd q bank balances $(haqqd keys show $WALLETNAME -a --bech acc)
break
;;

"Sync via stateSync")
SNAP_RPC="http://65.108.206.56:39658"
peers="e15e1867f68011f80f2763e6691c89c923bf2f24@78.46.16.236:41656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.haqqd/config/config.toml

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 500)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.haqqd/config/config.toml
haqqd tendermint unsafe-reset-all --home $HOME/.haqqd
wget -O $HOME/.haqqd/config/addrbook.json "https://raw.githubusercontent.com/NodesBlocks/Haqq-Network/main/addrbook.json"
systemctl restart haqqd && journalctl -u haqqd -f -o cat


break
;;

"Create validator")
haqqd tx staking create-validator \
  --amount 1000000000000000000aISLM \
  --from $WALLETNAME \
  --commission-max-change-rate "0.05" \
  --commission-max-rate "0.20" \
  --commission-rate "0.05" \
  --min-self-delegation "1" \
  --pubkey $(haqqd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $CHAIN_ID \
  --gas 300000 \
  -y
break
;;

"Request tokens")
echo "========================================================================================================================"
echo "Use Haqq discord faucet or check Hass docs"
echo "========================================================================================================================"

break
;;

"Exit")
exit
;;
*) echo "invalid option $REPLY";;
esac
done
done
