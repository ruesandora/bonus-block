<h1 align="center"> Bonus Block </h1>

![image](https://github.com/ruesandora/bonus-block/assets/101149671/499f1907-d4bf-4345-9b3f-0bdc1164f3d1)

<h1 align="center"> About BonusBlock </h1>

```
> I have been running the Bonus Block validator and testing the chain for about 2 months.
> I love the protocol I will continue to test Bonus Block.
```

<h1 align="center"> Updates and required packages </h1>

```sh
# Update system and install build tools
sudo apt update
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y

# Install Go
rm -rf $HOME/go
sudo rm -rf /usr/local/go
cd $HOME

curl https://dl.google.com/go/go1.20.1.linux-amd64.tar.gz | sudo tar -C/usr/local -zxvf -
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF

source $HOME/.profile
go version
```

<h1 align="center"> Installing the node </h1>

```sh
cd $HOME
rm -rf BonusBlock-chain/
git clone https://github.com/BBlockLabs/BonusBlock-chain

cd BonusBlock-chain/
git checkout v0.1.2

make install
bonus-blockd version
```

<h1 align="center"> Initalization processes </h1>

```sh
# Replace monikerName with your own nickname.
bonus-blockd init monikerName --chain-id=blocktopia-01

# Genesis
curl -Ls https://ss-t.bonusblock.nodestake.top/genesis.json > $HOME/.bonusblock/config/genesis.json 

# Addrbook
curl -Ls https://ss-t.bonusblock.nodestake.top/addrbook.json > $HOME/.bonusblock/config/addrbook.json
```

<h1 align="center"> Service file creation </h1>

```
sudo tee /etc/systemd/system/bonus-blockd.service > /dev/null <<EOF
[Unit]
Description=bonus-blockd Daemon
After=network-online.target
[Service]
User=$USER
ExecStart=$(which bonus-blockd) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable bonus-blockd
```

<h1 align="center"> Snapshot </h1>

```
SNAP_NAME=$(curl -s https://ss-t.bonusblock.nodestake.top/ | egrep -o ">20.*\.tar.lz4" | tr -d ">")
curl -o - -L https://ss-t.bonusblock.nodestake.top/${SNAP_NAME}  | lz4 -c -d - | tar -x -C $HOME/.bonusblock

sudo systemctl restart bonus-blockd
journalctl -u bonus-blockd -f
```

> After your node syncs:

```
# Please keep your wallet details
bonus-blockd keys add rues
```

> Then get the test token from [this](https://faucet.bonusblock.io/).

> If you sync, you can create a validator:

```
bonus-blockd tx staking create-validator \
--amount 900000ubonus \
--pubkey $(blockxd tendermint show-validator) \
--moniker "yourMonikerName" \
--identity "yourKeybaseId" \
--details "yourDetails" \
--website "yourWebsite" \
--chain-id blocktopia-01 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--from yourWalletName \
--gas-adjustment 1.4 \
--gas auto \
-y
```

> You can view yourself through explorer [here](https://explorer.nodestake.top/bonusblock-testnet/staking/bonusvaloper1du5pqppjfcrmdcm9js28sc6nqhvg4wfx6qfwck).











