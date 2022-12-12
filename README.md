# Gitopia-Node-Setup
You will learn how to setup Gitopia Node
+ Minimum System Requirements

CPU: 4 CPU

RAM: 8 GB

SSD: 200 GB

+ Updating Our Server

sudo apt-get update && sudo apt-get upgrade -y

+ Let's Download Required Libraries

sudo apt install curl build-essential git wget jq make gcc tmux chrony -y

+ Go Setup

cd
ver="1.18.5"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile
go version


+ Node Setup

curl https://get.gitopia.com | bash

+ Github Cloning

git clone -b v1.2.0 gitopia://gitopia/gitopia
cd gitopia && make install

+ We Create Our Moniker Name

GITOPIA_MONIKER="create a moniker name for you"

+ Configuration

gitopiad init --chain-id gitopia-janus-testnet-2 $GITOPIA_MONIKER

+ Gas Price Adjustment

sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.001utlore\"/" ~/.gitopia/config/app.toml

+ Adding Seed

sed -i 's#seeds = ""#seeds = "399d4e19186577b04c23296c4f7ecc53e61080cb@seed.gitopia.com:26656"#' $HOME/.gitopia/config/config.toml

+ Let's Open Puring

pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.gitopia/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.gitopia/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.gitopia/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.gitopia/config/app.toml

+ Let's Download Genesis File

wget https://server.gitopia.com/raw/gitopia/testnets/master/gitopia-janus-testnet-2/genesis.json.gz
gunzip genesis.json.gz
mv genesis.json $HOME/.gitopia/config/genesis.json

+ We Start the Service

tee /etc/systemd/system/gitopiad.service > /dev/null <<EOF
[Unit]
Description=Gitopia
After=network-online.target
[Service]
User=root
ExecStart=$(which gitopiad) start
Restart=always
RestartSec=3
LimitNOFILE=10000
[Install]
WantedBy=multi-user.target
EOF

+ We Create Screen

screen -S screenAdi 

+ We Are Starting

gitopiad start

+ Sync Boost

sudo systemctl stop gitopiad

cp $HOME/.gitopia/data/priv_validator_state.json $HOME/.gitopia/priv_validator_state.json.backup

gitopiad tendermint unsafe-reset-all --home $HOME/.gitopia

STATE_SYNC_RPC=https://gitopia-testnet.rpc.kjnodes.com:443
STATE_SYNC_PEER=d5519e378247dfb61dfe90652d1fe3e2b3005a5b@gitopia-testnet.rpc.kjnodes.com:41656
LATEST_HEIGHT=$(curl -s $STATE_SYNC_RPC/block | jq -r .result.block.header.height)
SYNC_BLOCK_HEIGHT=$(($LATEST_HEIGHT - 2000))
SYNC_BLOCK_HASH=$(curl -s "$STATE_SYNC_RPC/block?height=$SYNC_BLOCK_HEIGHT" | jq -r .result.block_id.hash)

sed -i.bak -e "s|^enable *=.*|enable = true|" $HOME/.gitopia/config/config.toml
sed -i.bak -e "s|^rpc_servers *=.*|rpc_servers = \"$STATE_SYNC_RPC,$STATE_SYNC_RPC\"|" \
  $HOME/.gitopia/config/config.toml
sed -i.bak -e "s|^trust_height *=.*|trust_height = $SYNC_BLOCK_HEIGHT|" \
  $HOME/.gitopia/config/config.toml
sed -i.bak -e "s|^trust_hash *=.*|trust_hash = \"$SYNC_BLOCK_HASH\"|" \
  $HOME/.gitopia/config/config.toml
sed -i.bak -e "s|^persistent_peers *=.*|persistent_peers = \"$STATE_SYNC_PEER\"|" \
  $HOME/.gitopia/config/config.toml
mv $HOME/.gitopia/priv_validator_state.json.backup $HOME/.gitopia/data/priv_validator_state.json

sudo systemctl start gitopiad && journalctl -u gitopiad -f --no-hostname -o cat

+ Creating a Wallet

gitopiad keys add "change here with a name"

+ Checking the Sync Status

gitopiad status 2>&1 | jq .SyncInfo

+ Faucet

https://gitopia.com/home

+ Creating a Validator

gitopiad tx staking create-validator \
  --amount 1999000utlore \
  --from your wallet name \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(gitopiad tendermint show-validator) \
  --moniker your moniker name \
  --chain-id gitopia-janus-testnet-2 \
  --fees 250utlore
  
  + Delegate
  
  gitopiad tx staking delegate <validatöradresi> 10000000utlore --from=cüzdanAdiniz --chain-id=gitopia-janus-testnet-2 --fees=250utlore
  
  
