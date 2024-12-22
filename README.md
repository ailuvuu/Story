Manual Installation
Official Documentation
Recommended Hardware: 6 Cores, 16GB RAM, 400GB of storage (NVME), 100 Mb/s

**install dependencies, if needed**
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

**install go, if needed**
```
cd $HOME
VER="1.22.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

**set vars**
```
echo "export MONIKER="test"" >> $HOME/.bash_profile
echo "export STORY_CHAIN_ID="odyssey-0"" >> $HOME/.bash_profile
echo "export STORY_PORT="52"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

**download binaries**
```
cd $HOME
wget -O geth https://github.com/piplabs/story-geth/releases/download/v0.11.0/geth-linux-amd64
chmod +x $HOME/geth
mv $HOME/geth ~/go/bin/
[ ! -d "$HOME/.story/story" ] && mkdir -p "$HOME/.story/story"
[ ! -d "$HOME/.story/geth" ] && mkdir -p "$HOME/.story/geth"
```

**install Story**
```
cd $HOME
rm -rf story
git clone https://github.com/piplabs/story
cd story
git checkout v0.13.0
go build -o story ./client 
mv $HOME/story/story $HOME/go/bin/
```

**init story app**
```
story init --moniker test --network odyssey
```

**set seeds and peers**
```
SEEDS="434af9dae402ab9f1c8a8fc15eae2d68b5be3387@story-testnet-seed.itrocket.net:29656"
PEERS="c2a6cc9b3fa468624b2683b54790eb339db45cbf@story-testnet-peer.itrocket.net:26656,16e5119da2cd37c561b65f645a202755518f8a6a@88.198.27.51:52656,7a6068313c4f605681dd35b35ad483f59862d46e@62.84.176.216:26656,c79b1b260b5502303605467f3c8b3e7457ece7a6@84.247.138.170:26656,a8b46ac679ccb1e0e78059a0b47a0aa677d617cd@75.119.137.180:26656,14ce9050665ffcf7a22e4164b6146a46f0cb210b@213.199.59.59:26656,3625d99f0be1fca7401115648bc8fe758583dca0@185.190.140.80:26656,0c34de4b876c36a08aaeb41cb64e62e79bf1aabc@45.67.221.236:26656,617641313e122203925fde55c1834f3131844585@176.57.188.122:26156,1560b5c395a993e950e3ebcdb180c55cc6dd5adb@84.247.163.14:26656,90d109237caf696705d302dc7d05273a42b118f0@84.247.166.228:26656,41ade4c9cb31ebad3f65d58ea862bdc119b87287@62.169.16.182:26656,af6907ee1da2fed3f286d01f7e6a7882ca9f0710@82.223.31.166:33556,0aa1939a71a83ec87cd239c6d934b8b06473da06@62.169.21.35:26656,c14d5f0f97197d39d9c73ee7af460216782e64c1@37.27.131.251:26656,69a0bad6288d0f629b4107dd4efc2948f06eb7fc@168.119.10.134:26656,8001a29fefdeca3c08ba5d577bd844b4f7a68c11@65.21.20.112:26656,bb26a3480f23f8b6f170404ea8238744f17ff1a2@149.50.117.96:26156,793b2289ef17b17b229a6487b13ee0ce08056328@162.19.102.6:26656,1c63647905963a0c95d904cd92ed7626bf1b8183@198.178.224.104:60730,caa2a9adbc2f0419d28cab2b11f3c91bd8789082@84.46.251.225:26656,3d74b86cf9b623ba9ec35c87b162c4edd6e1038c@51.159.20.96:36656,a54701a709ac3415c8f7c9a6d7b3dc4de2209b5e@194.238.26.192:26656,54843610ff11c214c097079b3492628a1a7286cd@195.26.251.60:26656,4838098901e80399d810311f9d9fea3fa3022cef@149.50.102.86:52656,4ed9844a7878eb8bf2802ff6e58aaa1b6c93375d@95.217.207.209:26656,11bf972a81b3488d4c7a7c2b9c04600eaf77ce88@185.215.165.34:26656,ead31037f63b7bf6e9004bc8ac7e2897470437df@52.89.231.138:26656,493e0dd839c3ffa0ce6899eea575dac9806223f5@51.161.13.62:26656,1ed5a845001e3a65a17365d9e344797e0b3764f1@176.9.8.81:20156,ae51a59fdd1addf65bdcb47358da15f684435955@49.12.130.151:26656"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.story/story/config/config.toml
```

# download genesis and addrbook
wget -O $HOME/.story/story/config/genesis.json https://server-3.itrocket.net/testnet/story/genesis.json
wget -O $HOME/.story/story/config/addrbook.json  https://server-3.itrocket.net/testnet/story/addrbook.json

# set custom ports in story.toml file
sed -i.bak -e "s%:1317%:${STORY_PORT}317%g;
s%:8551%:${STORY_PORT}551%g" $HOME/.story/story/config/story.toml

# set custom ports in config.toml file
sed -i.bak -e "s%:26658%:${STORY_PORT}658%g;
s%:26657%:${STORY_PORT}657%g;
s%:26656%:${STORY_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${STORY_PORT}656\"%;
s%:26660%:${STORY_PORT}660%g" $HOME/.story/story/config/config.toml

# enable prometheus and disable indexing
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.story/story/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.story/story/config/config.toml

# create geth servie file
sudo tee /etc/systemd/system/story-geth.service > /dev/null <<EOF
[Unit]
Description=Story Geth daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$HOME/go/bin/geth --odyssey --syncmode full --http --http.api eth,net,web3,engine --http.vhosts '*' --http.addr 0.0.0.0 --http.port ${STORY_PORT}545 --authrpc.port ${STORY_PORT}551 --ws --ws.api eth,web3,net,txpool --ws.addr 0.0.0.0 --ws.port ${STORY_PORT}546
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

# create story service file
sudo tee /etc/systemd/system/story.service > /dev/null <<EOF
[Unit]
Description=Story Service
After=network.target

[Service]
User=$USER
WorkingDirectory=$HOME/.story/story
ExecStart=$(which story) run

Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

# download snapshots
# backup priv_validator_state.json
cp $HOME/.story/story/data/priv_validator_state.json $HOME/.story/story/priv_validator_state.json.backup

# remove old data and unpack Story snapshot
rm -rf $HOME/.story/story/data
curl https://server-3.itrocket.net/testnet/story/story_2024-12-17_1323418_snap.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.story/story

# restore priv_validator_state.json
mv $HOME/.story/story/priv_validator_state.json.backup $HOME/.story/story/data/priv_validator_state.json

# delete geth data and unpack Geth snapshot
rm -rf $HOME/.story/geth/odyssey/geth/chaindata
mkdir -p $HOME/.story/geth/odyssey/geth
curl https://server-3.itrocket.net/testnet/story/geth_story_2024-12-17_1323418_snap.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.story/geth/odyssey/geth

# enable and start geth, story
sudo systemctl daemon-reload
sudo systemctl enable story story-geth
sudo systemctl restart story-geth && sleep 5 && sudo systemctl restart story

# check logs
journalctl -u story -u story-geth -f
Automatic Installation
source <(curl -s https://itrocket.net/api/testnet/story/story-autoinstall/)
Cosmovisor Setup
Install go, if needed:

cd $HOME
VER="1.22.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
Install and init Cosmovisor:

go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest
echo "export DAEMON_NAME="story"" >> $HOME/.bash_profile
echo "export DAEMON_HOME="$HOME/.story/story"" >> $HOME/.bash_profile
source $HOME/.bash_profile
cosmovisor init $(which story)
Create a directory and download the current version of story:

mkdir -p $HOME/.story/story/cosmovisor/upgrades/v0.13.0/bin
wget -O $HOME/.story/story/cosmovisor/upgrades/v0.13.0/bin/story https://github.com/piplabs/story/releases/download/v0.13.0/story-linux-amd64
chmod +x $HOME/.story/story/cosmovisor/upgrades/v0.13.0/bin/story
Update service file:

sudo tee /etc/systemd/system/story.service > /dev/null << EOF
[Unit]
Description=story node service
After=network-online.target

[Service]
User=$USER
Environment="DAEMON_NAME=story"
Environment="DAEMON_HOME=$HOME/.story/story"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="DAEMON_DATA_BACKUP_DIR=$HOME/.story/story/data"
ExecStart=$(which cosmovisor) run run
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
Enable and start Story using Cosmovisor:

sudo systemctl daemon-reload
sudo systemctl enable story
sudo systemctl restart story && sudo journalctl -u story -f
Congrats, you are now using Cosmovisor! 🎊
Create validator
View your validator key

story validator export
Export EVM private key

story validator export --export-evm-key
View EVM private key and make a key backup

cat $HOME/.story/story/config/private_key.txt
Use this private key to import your account into a wallet, e.g. Metamask or Phantom. Add the odyssey testnet to your wallet via faucet. Then, copy your 'EVM address' from the wallet and request $IP tokens. Now you can see the balance and make transactions in the wallet app.

Before creating a validator, wait for your node to get fully synced. Once "catching_up" is "false", move on to the next step
curl localhost:$(sed -n '/\[rpc\]/,/laddr/ { /laddr/ {s/.*://; s/".*//; p} }' $HOME/.story/story/config/config.toml)/status | jq
Create validator
story validator create --stake 1500000000000000000000 --moniker $MONIKER --chain-id 1516 --private-key $(cat $HOME/.story/story/config/private_key.txt | grep "PRIVATE_KEY" | awk -F'=' '{print $2}')
Remember to backup your validator priv_key from here:

cat $HOME/.story/story/config/priv_validator_key.json
Firewall rules
Configure firewall rules:

sudo ufw allow 30303/tcp comment geth_p2p_port
sudo ufw allow 26656/tcp comment story_p2p_port
Delete node
sudo systemctl stop story story-geth
sudo systemctl disable story story-geth
rm -rf $HOME/.story
sudo rm /etc/systemd/system/story.service /etc/systemd/system/story-geth.service
sudo systemctl daemon-reload
