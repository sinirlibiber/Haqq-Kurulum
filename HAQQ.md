#!/bin/bash
echo -e "\033[0;31m"
echo "==================================================";
echo "     █▀▀▀ █░░█ █▀▄▀█ █░░█ █▀▀ █▀▀▄ █▀▀ █░░█       ";
echo "     █░▀█ █░░█ █░▀░█ █░░█ ▀▀█ █▀▀▄ █▀▀ █▄▄█ 		";
echo "     ▀▀▀▀ ░▀▀▀ ▀░░░▀ ░▀▀▀ ▀▀▀ ▀▀▀░ ▀▀▀ ▄▄▄█    	";
echo -e "\e[0m"
echo "=================================================="                                  


sleep 2

# DEGISKENLER by gumusbey
HAQQ_WALLET=wallet
HAQQ=haqqd
HAQQ_ID=haqq_54211-2
HAQQ_PORT=29
HAQQ_FOLDER=.haqqd
HAQQ_FOLDER2=haqq
HAQQ_VER=v1.0.3
HAQQ_REPO=https://github.com/haqq-network/haqq
HAQQ_GENESIS=https://raw.githubusercontent.com/haqq-network/validators-contest/master/genesis.json
HAQQ_ADDRBOOK=
HAQQ_MIN_GAS=0
HAQQ_DENOM=aISLM
HAQQ_SEEDS=62bf004201a90ce00df6f69390378c3d90f6dd7e@seed2.testedge2.haqq.network:26656,23a1176c9911eac442d6d1bf15f92eeabb3981d5@seed1.testedge2.haqq.network:26656
HAQQ_PEERS=b3ce1618585a9012c42e9a78bf4a5c1b4bad1123@65.21.170.3:33656,952b9d918037bc8f6d52756c111d0a30a456b3fe@213.239.217.52:29656,85301989752fe0ca934854aecc6379c1ccddf937@65.109.49.111:26556,d648d598c34e0e58ec759aa399fe4534021e8401@109.205.180.81:29956,f2c77f2169b753f93078de2b6b86bfa1ec4a6282@141.95.124.150:20116,eaa6d38517bbc32bdc487e894b6be9477fb9298f@78.107.234.44:45656,37513faac5f48bd043a1be122096c1ea1c973854@65.108.52.192:36656,d2764c55607aa9e8d4cee6e763d3d14e73b83168@66.94.119.47:26656,fc4311f0109d5aed5fcb8656fb6eab29c15d1cf6@65.109.53.53:26656,297bf784ea674e05d36af48e3a951de966f9aa40@65.109.34.133:36656,bc8c24e9d231faf55d4c6c8992a8b187cdd5c214@65.109.17.86:32656

sleep 1

echo "export HAQQ_WALLET=${HAQQ_WALLET}" >> $HOME/.bash_profile
echo "export HAQQ=${HAQQ}" >> $HOME/.bash_profile
echo "export HAQQ_ID=${HAQQ_ID}" >> $HOME/.bash_profile
echo "export HAQQ_PORT=${HAQQ_PORT}" >> $HOME/.bash_profile
echo "export HAQQ_FOLDER=${HAQQ_FOLDER}" >> $HOME/.bash_profile
echo "export HAQQ_FOLDER2=${HAQQ_FOLDER2}" >> $HOME/.bash_profile
echo "export HAQQ_VER=${HAQQ_VER}" >> $HOME/.bash_profile
echo "export HAQQ_REPO=${HAQQ_REPO}" >> $HOME/.bash_profile
echo "export HAQQ_GENESIS=${HAQQ_GENESIS}" >> $HOME/.bash_profile
echo "export HAQQ_PEERS=${HAQQ_PEERS}" >> $HOME/.bash_profile
echo "export HAQQ_SEED=${HAQQ_SEED}" >> $HOME/.bash_profile
echo "export HAQQ_MIN_GAS=${HAQQ_MIN_GAS}" >> $HOME/.bash_profile
echo "export HAQQ_DENOM=${HAQQ_DENOM}" >> $HOME/.bash_profile
source $HOME/.bash_profile

sleep 1

if [ ! $HAQQ_NODENAME ]; then
	read -p "NODE ISMI YAZINIZ: " HAQQ_NODENAME
	echo 'export HAQQ_NODENAME='$HAQQ_NODENAME >> $HOME/.bash_profile
fi

echo -e "NODE ISMINIZ: \e[1m\e[32m$HAQQ_NODENAME\e[0m"
echo -e "CUZDAN ISMINIZ: \e[1m\e[32m$HAQQ_WALLET\e[0m"
echo -e "CHAIN ISMI: \e[1m\e[32m$HAQQ_ID\e[0m"
echo -e "PORT NUMARANIZ: \e[1m\e[32m$HAQQ_PORT\e[0m"
echo '================================================='

sleep 2


# GUNCELLEMELER by gumusbey
echo -e "\e[1m\e[32m1. GUNCELLEMELER YUKLENIYOR... \e[0m" && sleep 1
sudo apt update && sudo apt upgrade -y


# GEREKLI PAKETLER by gumusbey
echo -e "\e[1m\e[32m2. GEREKLILIKLER YUKLENIYOR... \e[0m" && sleep 1
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y

# GO KURULUMU by gumusbey
echo -e "\e[1m\e[32m1. GO KURULUYOR... \e[0m" && sleep 1
ver="1.18.2"
cd $HOME
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
source ~/.bash_profile
go version

sleep 1

# KUTUPHANE KURULUMU by gumusbey
echo -e "\e[1m\e[32m1. REPO YUKLENIYOR... \e[0m" && sleep 1
cd $HOME
git clone -b $HAQQ_VER $HAQQ_REPO
cd $HAQQ_FOLDER2
make install

sleep 1

# KONFIGURASYON by gumusbey
echo -e "\e[1m\e[32m1. KONFIGURASYONLAR AYARLANIYOR... \e[0m" && sleep 1
$HAQQ config chain-id $HAQQ_ID
$HAQQ config keyring-backend file
$HAQQ init $HAQQ_NODENAME --chain-id $HAQQ_ID

# ADDRBOOK ve GENESIS by gumusbey
wget $HAQQ_GENESIS -O $HOME/$HAQQ_FOLDER/config/genesis.json
wget $HAQQ_ADDRBOOK -O $HOME/$HAQQ_FOLDER/config/addrbook.json

# EŞLER VE TOHUMLAR by gumusbey
SEEDS="$HAQQ_SEEDS"
PEERS="$HAQQ_PEERS"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/$HAQQ_FOLDER/config/config.toml

sleep 1


# config pruning
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/$HAQQ_FOLDER/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/$HAQQ_FOLDER/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/$HAQQ_FOLDER/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/$HAQQ_FOLDER/config/app.toml


# ÖZELLEŞTİRİLMİŞ PORTLAR by gumusbey
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${HAQQ_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${HAQQ_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${HAQQ_PORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${HAQQ_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${HAQQ_PORT}660\"%" $HOME/$HAQQ_FOLDER/config/config.toml
sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${HAQQ_PORT}317\"%; s%^address = \":8080\"%address = \":${HAQQ_PORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${HAQQ_PORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${HAQQ_PORT}091\"%" $HOME/$HAQQ_FOLDER/config/app.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${HAQQ_PORT}657\"%" $HOME/$HAQQ_FOLDER/config/client.toml

# PROMETHEUS AKTIVASYON by gumusbey
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/$HAQQ_FOLDER/config/config.toml

# MINIMUM GAS AYARI by gumusbey
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.00125$HAQQ_DENOM\"/" $HOME/$HAQQ_FOLDER/config/app.toml

# INDEXER AYARI by gumusbey
indexer="null" && \
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/$HAQQ_FOLDER/config/config.toml

# RESET by gumusbey
$HAQQ tendermint unsafe-reset-all --home $HOME/$HAQQ_FOLDER

echo -e "\e[1m\e[32m4. SERVIS BASLATILIYOR... \e[0m" && sleep 1
# create service
sudo tee /etc/systemd/system/$HAQQ.service > /dev/null <<EOF
[Unit]
Description=$HAQQ
After=network.target
[Service]
Type=simple
User=$USER
ExecStart=$(which $HAQQ) start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF


# SERVISLERI BASLAT by gumusbey
sudo systemctl daemon-reload
sudo systemctl enable $HAQQ
sudo systemctl restart $HAQQ

echo '=============== KURULUM TAMAM! by gumusbey ==================='
echo -e 'LOGLARI KONTROL ET: \e[1m\e[32mjjournalctl -fu haqqd -o cat\e[0m'
echo -e "SENKRONIZASYONU KONTROL ET: \e[1m\e[32mcurl -s localhost:${HAQQ_PORT}657/status | jq .result.sync_info\e[0m"

source $HOME/.bash_profile
