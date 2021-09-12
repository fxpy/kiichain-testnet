# Preconfig

### On Kichain Server:
```nano /root/kichain/kid/config/config.toml```
Check the rpc laddr section.
If 127.0.0.1:26657 change to 0.0.0.0:26657

Restart kichain service to apply
```
systemctl restart kichaind
```

# Install Cosmos Relayer
### On Umee Server:
```
git clone https://github.com/cosmos/relayer.git
cd relayer
git checkout v0.9.3
make install
```

## Check relay ver
```
rly version
```
> version: v0.9.3
> commit: 4b81fa59055e3e94520bdfae1debe2fe0b747dc1
> cosmos-sdk: v0.42.4
> go: go1.15.11 linux/amd64


## Init rly config
```
rly config init
```

## Add chain configs
Edit config.yaml (Insert your kichain server's IP)

```nano $HOME/.relayer/configs/config.yaml```

```
global:
  api-listen-addr: :5183
  timeout: 3m
  light-cache-size: 20
chains:
- key:
  chain-id: umee-betanet-1
  rpc-addr: http://127.0.0.1:26657
  account-prefix: umee
  gas-adjustment: 1.5
  gas-prices: 0.025uumee
  trusting-period: 10m
- key:
  chain-id: kichain-t-4
  rpc-addr: http://__.__.__.__:26657
  account-prefix: tki
  gas-adjustment: 1.5
  gas-prices: 0.025utki
  trusting-period: 10m
paths: {}
```

## Add chains to relay
```
rly chains add -f kichain.json
rly chains add -f umee.json
```
# ADD WALLETS
## Add umee key to relay and write down output
```
rly keys add umee-betanet-1 umeek
```

## Restore kichain key
```
rly keys restore kichain-t-4 kichk "mnemonics"
```

## Add key to chain
```
rly chains edit umee-betanet-1 key umeek
rly chains edit kichain-t-4 key kichk
```

# Fund balance

### umeed tx bank send <FROM_key_or_address> <TO_umee_address_relayer> <AMOUNT>uumee --chain-id umee-betanet-1 --gas=auto --fees=1000uume
```
umeed tx bank send $UMEE_NODE_WALLET umee1ye6tqg8erau6yuz9fpr2wt043myrsr0ps3avu3 1000000uumee --chain-id umee-betanet-1 --gas=auto --fees=1000uumee
```

# Check balance
```
rly q balance umee-betanet-1
rly q bal kichain-t-4
```

# Init light client
```
rly light init umee-betanet-1 -f
rly light init kichain-t-4 -f
```

# Generate path Umee to Kichain
```
rly paths generate umee-betanet-1 kichain-t-4 umee_to_ki --port=transfer
```
> I[2021-09-11|10:40:17.528] ★ Channel created: [umee-betanet-1]chan{channel-7}port{transfer} ->  [kichain-t-4]chan{channel-167}port{transfer}
  
# Check preconfig
```
rly chains list
```

# Open TX link
```
rly tx link umee_to_ki
```

# Send tokens Umee to Kichain
```
rly tx transfer umee-betanet-1 kichain-t-4 100000uumee tki1h4qz3864q0yxjequsja5qrvemce3znrrfezy2r --path umee_to_ki
```
  
# Generate path Kichain to Umee
```
rly paths generate kichain-t-4 umee-betanet-1 ki_to_umee --port=transfer
```
> I[2021-09-11|10:51:48.618] ★ Channel created: [kichain-t-4]chan{channel-167}port{transfer} -> [umee-betanet-1]chan{channel-7}port{transfer} 
  
# Open Channel KI to UMEE
```
rly tx link ki_to_umee
```

# Send Tokens Umee to Kichain
```
rly tx transfer kichain-t-4 umee-betanet-1 100000utki umee1ye6tqg8erau6yuz9fpr2wt043myrsr0ps3avu3 --path ki_to_umee
```
