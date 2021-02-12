This guide is written for anyone looking to run a opendex liquidity provider setup entirely via the command line and create a revenue stream via automated arbitrage.

# Prerequisites

## Two Modes

1. **Default: Light setup** using [Neutrino](https://github.com/lightninglabs/neutrino) and a random open eth provider. This keeps the setup light-weight & cheap, but creates a certain dependency on other people's full nodes.
2. **Optional: Full setup** using [bitcoind](https://github.com/bitcoin/bitcoin/), [litecoind](https://github.com/litecoin-project/litecoin) and [geth](https://github.com/ethereum/go-ethereum). Requires more time and resources, but keeps the setup trustless.

## Three Networks

1. **Simnet**. `Status: down` until further notice

2. **Testnet**. `Status: up | Required CPUs: 2 for light, 4 for full | RAM: 2GB for light, 16GB for full | Disk: 1GB for light, 200GB for full | Initial Sync Time: 15 mins for light, 24h for full`

    bitcoin testnet 3, litecoin testnet 4, ethereum rinkeby. Faucets: [t-BTC](https://coinfaucet.eu/en/btc-testnet/), [t-LTC](https://testnet.help/en/ltcfaucet/testnet), [t-ETH 1](https://faucet.rinkeby.io/) or [2](https://testnet.help/en/ethfaucet/rinkeby). If you need help or some testnet coins, hit us up on [Discord](https://discord.gg/RnXFHpn)!

3. **Mainnet**. `Status: down | Required CPUs: 2 for light, 4 for full | RAM: 2GB for light, 16GB for full | Disk: 1GB for light, 1TB for full | Initial Sync Time: 30 mins for light, 72h for full`
    
   Down until all breaking changes are merged and some weeks on testnet didn't reveal major issues.


## Hardware
Since liquidity providers should be online 24/7 and we are ushering in a post-cloud era, we recommend setting up a power-efficient linux box connected to your router. No special configurations, like port forwardings, are necessary. Running your opendexd setup in the cloud is obviously possible, just not something we encourage to do.

[**🧑‍🏭 Standard Hardware Guide**](Standard%20Hardware%20Guide.md): This guide walks you through setting up an arm64-based Raspberry Pi3/4. Costs: **65€-290€**

[**💪 Pro Hardware Guide**](Pro%20Hardware%20Guide.md): This guide walks you through setting up a powerful amd64-based Mini PC. Costs: **180€-465€**

🎚️ **Custom**: If you are using a different device or a cloud VPS:
  * Check the hardware requirements for the different networks and modes above
  * The full setup requires a SSD for geth being able to sync. For the light setup, a regular HDD/SD card is fine.
  * If you are using a VPS for testnet or mainnet, you can switch to 2 cores & 4 GB RAM after initial sync, given you use default settings.
  * We currently support `amd64` (also called `x86`/`x64`) and `arm64` (also called `aarch64`/`armv8`), which should cover most devices and services.

## Software

Docker & Docker Compose.

Version >= 18.09 on Linux or Windows 10 [using WSL 2](https://docs.microsoft.com/en-us/windows/wsl/install-win10). If you do not have docker & docker-compose installed yet and you are using Ubuntu 20.04 LTS, install these via `sudo apt install docker.io`. If you are using any version besides Ubuntu 20.04, follow the official [docker install instructions](https://docs.docker.com/get-docker/). Also make sure that the current user can run docker commands. Test with `docker run hello-world`. If this fails, [follow these instructions](https://docs.docker.com/engine/install/linux-postinstall/). This guide was written using Ubuntu 20.04 LTS.

# The Setup

From here we assume that your device is running with docker set up. Check the guides in the hardware section above if your device is not ready yet.

## Let's Roll

Start the environment with

```bash
curl https://raw.githubusercontent.com/opendexnetwork/opendex-docker/master/opendexd.sh -o ~/opendexd.sh
bash ~/opendexd.sh
```
The setup will ask you to choose the network:
```
1) Simnet
2) Testnet
3) Mainnet
Please choose the network: 3
🚀 Launching mainnet environment
🌍 Checking for updates ...
```
Sync light clients (default):
```
Syncing light clients:
┌─────────┬─────────────────────────────────────────────────────┐
│ SERVICE │ STATUS                                              │
├─────────┼─────────────────────────────────────────────────────┤
│ lndbtc  │ Syncing 34.24% (610000/1781443)                     │
├─────────┼─────────────────────────────────────────────────────┤
│ lndltc  │ Syncing 12.17% (191000/1568645)                     │
└─────────┴─────────────────────────────────────────────────────┘
```
And then guide you through some basics:
```
Do you want to create a new opendexd environment or restore an existing one?
1) Create New
2) Restore Existing
Please choose: 1
```
When creating a new opendexd SEED, the setup asks you to set a password to encrypt your environment's private keys and to write down your mnemonic phrase. This serves as backup for your opendexd node key and wallets (your on-chain assets). This is your last resort in case something happens to your device. **Keep it somewhere safe!**

```
You are creating an opendexd node key and underlying wallets. All will be secured by a single password provided below.
  
Enter a password: 
Re-enter password: 

----------------------BEGIN OPENDEX SEED---------------------
 1. you         2. won't       3. find        4. money      
 5. in          6. this        7. seed        8. but    
 9. good       10. thinking   11. if         12. you      
13. are        14. interested 15. in         16. getting     
17. rewarded   18. for        19. testing    20. opendex  
21. security   22. hit        23. us         24. up   
-----------------------END OPENDEX SEED----------------------

The following wallets were initialized: BTC, LTC, ERC20(ETH)
```

Then you'll be asked to enter the path to your backup drive, e.g. a previously mounted USB drive:
```
Please enter a path to a destination where to store a backup of your environment. It includes everything, but NOT your on-chain wallet balance which is secured by your opendexd SEED. The path should be an external drive, like a USB or network drive, which is permanently available on your device since backups are written constantly.

Enter path to backup location: /media/USB/
Checking... OK.
```

The entered backup drive location is persisted as `backup-dir = "/media/USB/"` in `mainnet.conf` and can be changed any time. Alternatively, you can consider running your environment on two hard drives in [RAID 1](https://en.wikipedia.org/wiki/Standard_RAID_levels#RAID_1) to protect against data loss.

Then the setup might restart clients and ask you to enter your password once more before the CTL

Use the `status` command to check on the your setup's health and sync progress. The default light setup should show `Ready` after some seconds:
```
mainnet > status
┌───────────┬────────────────────────────────────────────────┐
│ SERVICE   │ STATUS                                         │
├───────────┼────────────────────────────────────────────────┤
│ bitcoind  │ Ready (light mode)                             │
├───────────┼────────────────────────────────────────────────┤
│ litecoind │ Ready (light mode)                             │
├───────────┼────────────────────────────────────────────────┤
│ geth      │ Ready (light mode)                             │
├───────────┼────────────────────────────────────────────────┤
│ lndbtc    │ Syncing                                        │
├───────────┼────────────────────────────────────────────────┤
│ lndltc    │ Syncing                                        │
├───────────┼────────────────────────────────────────────────┤
│ connext   │ Ready                                          │
├───────────┼────────────────────────────────────────────────┤
│ opendexd  │ Waiting for lndbtc, lndltc                     │
└───────────┴────────────────────────────────────────────────┘
```

If you configured the full setup via config file or cli parameters, the sync will start fast and get slower towards the end. You might see 0.00% progress for several minutes at first.
```
mainnet > status
┌───────────┬────────────────────────────────────────────────┐
│ SERVICE   │ STATUS                                         │
├───────────┼────────────────────────────────────────────────┤
│ bitcoind  │ Syncing 0.00% (0/436000)                       │
├───────────┼────────────────────────────────────────────────┤
│ litecoind │ Syncing 0.00% (0/324000)                       │
├───────────┼────────────────────────────────────────────────┤
│ geth      │ Syncing 0.00% (55/9140561)                     │
├───────────┼────────────────────────────────────────────────┤
│ lndbtc    │ Waiting for sync                               │
├───────────┼────────────────────────────────────────────────┤
│ lndltc    │ Waiting for sync                               │
├───────────┼────────────────────────────────────────────────┤
│ connext   │ Waiting for sync                               │
├───────────┼────────────────────────────────────────────────┤
│ opendexd  │ Waiting for sync                               │
└───────────┴────────────────────────────────────────────────┘
```

After a while you should see all three full-nodes syncing nicely.
```
mainnet > status
┌───────────┬────────────────────────────────────────────────┐
│ SERVICE   │ STATUS                                         │
├───────────┼────────────────────────────────────────────────┤
│ bitcoind  │ Syncing 43.06% (262348/609123)                 │
├───────────┼────────────────────────────────────────────────┤
│ litecoind │ Syncing 35.94% (631593/1757002)                │
├───────────┼────────────────────────────────────────────────┤
│ geth      │ Syncing 10.16% (929072/9140623)                │
├───────────┼────────────────────────────────────────────────┤
│ lndbtc    │ Waiting for sync                               │
├───────────┼────────────────────────────────────────────────┤
│ lndltc    │ Waiting for sync                               │
├───────────┼────────────────────────────────────────────────┤
│ connext   │ Ready                                          │
├───────────┼────────────────────────────────────────────────┤
│ opendexd  │ Waiting for sync                               │
└───────────┴────────────────────────────────────────────────┘
```
Bitcoind/Litecoind should finish syncing within 12h, geth in about 72h on powerful hardware. A Pi4 needs about twice that long.


The CLI takes `opendex-cli` commands without the need to prepend `opendex-cli`, e.g. simply type `getinfo` to get basic information about your opendex node. Run `help` to get an always up-to-date list of commands. Append `-j` to any command to get JSON instead of the formatted output, e.g. using `listpeers` to see other opendexd nodes on the network:

```
mainnet > listpeers -j
{
  "peersList": [
    {
      "address": "rgz5icb5jdxzmu7r7tbis64q23ioytzd4tqikuyb5kz75w75rbe6veyd.onion:8885",
      "nodePubKey": "02529a91d073dda641565ef7affccf035905f3d8c88191bdea83a35f37ccce5d64",
      "lndPubKeysMap": [
        [
          "BTC",
          "035cb9afb06a83e65fbab15c900d78580673cf56ce38c5814fb71f1eb57fcba7ee"
        ],
        [
          "LTC",
          "036cf16cd7de6193efb2855e784409c3633f893662dd6edcf7a545a99659232373"
        ]
      ],
      "inbound": false,
      "pairsList": [
        "LTC/BTC",
        "ETH/BTC",
      ],
      "opendexdVersion": "1.2.7",
      "secondsConnected": 100,
      "connextAddress": "0xe802431257a1d9366BD5747F0F52bAd25A6C3092"
    }
  ]
}
```

## Your First Trade

Start by depositing some funds into your opendex node: 

```bash
deposit btc #Send BTC to this address
deposit ltc #Send LTC to this address
deposit eth #Send ETH to this address
```

The deposit command for BTC & LTC is powered by [Boltz](https://boltz.exchange). Boltz will automatically open a balanced lightning channel to you, if you don't have a channel yet. This can take several minutes to complete and we'd kindly ask you to wait patiently for your funds to appear in the `getbalance` overview. If you want to follow what is happening under the hood, you can do so by typing `logs boltz`. For ETH, currently one still needs to trigger a manual channel creation in a second step after funds were deposited:
```
openchannel ETH 13.37
```

Check existing orders for all activated pairs with the command `orderbook`. It might take several seconds to see orders after opendexd was started due to the decentralized nature of the order exchange. Use `orderbook btc/usdt` to show the order book for BTC/USDT only:

```
mainnet > orderbook btc/usdt

Trading pair: BTC/USDT
┌───────────────────────────────────────┬───────────────────────────────────────┐
│ Buy                                   │ Sell                                  │
├───────────────────┬───────────────────┼───────────────────┬───────────────────┤
│ Quantity          │ Price             │ Price             │ Quantity          │
├───────────────────┼───────────────────┼───────────────────┼───────────────────┤
│ 0.28918298        │ 7171.56           │ 7172.253          │ 0.1               │
├───────────────────┼───────────────────┼───────────────────┼───────────────────┤
│ 1                 │ 7171.1937         │ 7172.9757         │ 0.1               │
├───────────────────┼───────────────────┼───────────────────┼───────────────────┤
│ 0.1               │ 7171.083          │ 7316.0663         │ 1                 │
├───────────────────┼───────────────────┼───────────────────┼───────────────────┤
│ 0.1               │ 7170.899          │ 7316.44           │ 0.22393946        │
└───────────────────┴───────────────────┴───────────────────┴───────────────────┘
```

Use `getbalance` to check your balance *before* the swap.

```
mainnet > getbalance

Balance:
┌──────────┬───────────────┬────────────────────────────┬───────────────────────────────┐
│ Currency │ Total Balance │ Channel Balance (Tradable) │ Wallet Balance (Not Tradable) │
├──────────┼───────────────┼────────────────────────────┼───────────────────────────────┤
│ BTC      │ 6.10944853    │ 2.5                        │ 3.60944853                    │
├──────────┼───────────────┼────────────────────────────┼───────────────────────────────┤
│ USDT     │ 5000          │ 5000                       │ 0                             │
├──────────┼───────────────┼────────────────────────────┼───────────────────────────────┤
│ LTC      │ 21            │ 11                         │ 10                            │
├──────────┼───────────────┼────────────────────────────┼───────────────────────────────┤
│ ETH      │ 500           │ 500                        │ 0                             │
└──────────┴───────────────┴────────────────────────────┴───────────────────────────────┘
```

Issue a regular limit order with e.g. `sell 0.1 btc/usdt 7171` to sell 0.1 btc for a price of 7171 USDT per BTC. If your order was matched, settlement shouldn't take longer than a couple of seconds. 
```
mainnet > sell 0.1 btc/usdt 7171
swapped 0.1 BTC with peer order ca24fe00-1c1e-11ea-8b1b-3b2ec0335696
```

Use `getbalance` to check your balance *after* the swap. You are now owning 0.1 BTC less and 717 USDT more.

```
mainnet > getbalance

Balance:
┌──────────┬───────────────┬────────────────────────────┬───────────────────────────────┐
│ Currency │ Total Balance │ Channel Balance (Tradable) │ Wallet Balance (Not Tradable) │
├──────────┼───────────────┼────────────────────────────┼───────────────────────────────┤
│ BTC      │ 6.00944842    │ 2.39999989                 │ 3.60944853                    │
├──────────┼───────────────┼────────────────────────────┼───────────────────────────────┤
│ USDT     │ 5717          │ 5717                       │ 0                             │
├──────────┼───────────────┼────────────────────────────┼───────────────────────────────┤
│ LTC      │ 21            │ 11                         │ 10                            │
├──────────┼───────────────┼────────────────────────────┼───────────────────────────────┤
│ ETH      │ 500           │ 500                        │ 0                             │
└──────────┴───────────────┴────────────────────────────┴───────────────────────────────┘
```

## Connect Arby

In this final step we are connecting your opendex setup to your CEX (Centralized EXchage) account via a liquidity provider bot called ["arby"](https://github.com/opendexnetwork/market-maker-bot). Arby enables "transfer" of orders from the CEX into OpenDEX and creates an arbitrage revenue stream for you as liquidity provider. Arby issues orders on the OpenDEX network based on the CEX price, adding a `margin` as premium. When orders are filled on OpenDEX, arby takes care of executing a counter trade on the CEX to lock in profits. At the time of writing, arby supports connecting to [Binance](https://www.binance.com) and [Kraken](https://www.kraken.com/), but more exchanges will be added over time; check arby's [FAQ](https://github.com/opendexnetwork/market-maker-bot#faq) for an up-to-date list. We'll use Binance as example in the following. You will need funds for at least one supported asset on Binance (e.g. BTC) for arby to start issuing orders. To activate arby, `exit` from `opendexd ctl` and run `cp ~/.opendexd-docker/mainnet/sample-mainnet.conf ~/.opendexd-docker/mainnet/mainnet.conf` to create a config file for your environment. Then edit the following options in `mainnet.conf`:
```bash
opendexd@ubuntu:~$ nano ~/.opendex-docker/mainnet/mainnet.conf
# this option needs to be set to false to allow arby to execute Binance orders on your behalf, crucially needed for arby to function
test-mode="false"
# the trading pair to activate arby for; currently arby can only handle one pair at a time
base-asset = "BTC"
quote-asset = "USDT"
#cex-base-asset = "" # optional - only needs to be specified if centralized exchange base asset is different from base-asset, e.g. USD instead of USDT
#cex-quote-asset = "" # optional - only needs to be specified if centralized exchange quote asset is different from quote-asset, e.g. USD instead of USDT
# log into your Binance account to obtain your api key and secret
cex = "binance"
cex-api-key = "your api key"
cex-api-secret = "your api secret"
# this is the percentage you'd like to add on top of your orders, 3% in this example
margin = "0.03"
# enable arby
disabled = false
# CTRL+S, CTRL+X.
```

Re-enter opendex-ctl (`bash ~/opendexd.sh`) and accept the prompt to add arby. After a minute you should see arby's automatically issued orders based on your Binance and OpenDEX balance via `listorders`. Completed OpenDEX trades are listed in `tradehistory`. You can follow actions taken by arby with `logs arby`.

Check the official [README](https://github.com/opendexnetwork/market-maker-bot/blob/main/README.md) to learn more about how arby works.


# Report Issues

Please give us feedback and report bugs by running `report` from within `opendex ctl` or join our dedicated "-help" channel on [Discord](https://discord.gg/RnXFHpn)!

# Tips 'n Tricks
* No need to open/forward ports
* An overview of all available commands within `opendex ctl` can be printed by typing `help` in `opendex ctl`. It allows to use client's cli (e.g. `lncli`), check client status, logs and many more.
* The opendex-docker setup uses the fixed home directory `~/.opendex-docker` where blockchain & wallet data is stored in by default. Customize the wallet & chain data directory by creating a global opendex-docker config file with `cp ~/.opendex-docker/sample-opendex-docker.conf ~/.opendex-docker/opendex-docker.conf`, then edit `dir`.
* All config options can temporary be set via cli parameters; run `bash opendex.sh --help` to get an overview of all available parameters. To e.g. use another directory for your mainnet environment, you can run `bash opendex.sh --mainnet-dir /path/to/temp/mainnet/dir`.
* To permanently change options on a network level, create a network-specific config file with the latest options, e.g. for mainnet with `cp ~/.opendex-docker/mainnet/sample-mainnet.conf ~/.opendex-docker/mainnet/mainnet.conf`, then edit `mainnet.conf`.
* If you only have a small SSD available (<300GB) for a full setup, you can place your entire setup on a HDD, except for a small part of geth's data, which needs to be located on a fast SSD:
```bash
[geth]
# SSD (internal)
dir = "/home/<user>/.opendex-docker/mainnet/geth"
# HDD (external)
ancient-chaindata-dir = "/media/HDD/opendex/03-Mainnet/data/geth"
```
* Sample config full setup:
```bash
# edit these lines to sync full nodes for bitcoin, litecoin & ethereum
[bitcoind]
mode = "native"
[litecoind]
mode = "native"
[geth]
mode = "native"
```
* You may use external full-nodes (including infura).  
```bash
# connect to an external bitcoin core node in your local network (Use `10.0.2.1` on linux or `host.docker.internal` on mac if the full node is running on the same machine)
[bitcoind]
mode = "external"
rpc-host = "192.168.1.42"
rpc-port = "8332"
rpc-user = "opendex"
rpc-password = "opendex"
zmqpubrawblock = "192.168.1.42:28332"
zmqpubrawtx = "192.168.1.42:28333"
```
* Sample config of your external bitcoind/litecoind to work with the defaults in the `<network>.conf` file:
```bash
-rpcuser=opendex
-rpcpassword=opendex
-rpcport=18332
-rpcallowip=0.0.0.0/0
-rpcbind=0.0.0.0
-zmqpubrawblock=tcp://0.0.0.0:38332
-zmqpubrawtx=tcp://0.0.0.0:38333
```
* Permanently set the alias `opendex` to launch `opendex ctl` from anywhere:
Add the line `alias opendex="bash ~/opendex.sh"` to the end of `~/.bashrc` or `~/.bash_aliases` on Linux and `bash_profile` on Mac, then `source` the file.
* You can `exit` from `opendex ctl` any time and re-enter with `bash ~/opendex.sh`; the environment will stay up.
* A reboot of your host machine does **not** restart your `opendex-docker` environment by default. You will need to run `bash ~/opendex.sh` and `unlock` your environment with your password.
* Permanently stop the environment by typing `down` in `opendex ctl`. A restart can be achieved with `down` first and then running `bash ~/opendex.sh` again.
* `opendex-docker` only uses offical opendexd releases for mainnet. Testnet is running the latest `opendexd` master and is updated frequently.
* If you are syncing the full setup, and `geth` shows sync status **99.99%** for longer than 72h, you are probably running geth on a drive that is too slow for geth to catch up with the chain. In this case, `down` the environment and run a performance test of the disk as desribed [here](Standard%20Hardware%20Guide.md#pi-full-setup). If results are below the 100 MB/s mark, you can either switch to a faster SSD, use the default light setup connecting to an open geth node or use infura.
* Docker *might* not play nicely with a VPN you are running on the host machine. If you see `Failed to launch environment`, try disconnecting the VPN.
* If you decide to remove `opendex-docker` from your machine, run the following commands when the environment is `down`:
```bash
# Use with caution: this step removes all `opendex` blockchain and wallet data from your system. If you have channels open without backup or lost your seed mnemonic, you are at risk of loosing funds.
sudo rm -rf ~/.opendex-docker
rm -rf ~/opendex.sh
rm -rf /custom/mainnet/dir
```

## References
* [bitcoind config options](https://github.com/bitcoin/bitcoin/blob/master/share/examples/bitcoin.conf)
* [litecoind config options](https://litecoin.info/index.php/Litecoin.conf#litecoin.conf_Configuration_File)
* [geth config options](https://github.com/ethereum/go-ethereum/blob/master/README.md#configuration)
* [lnd config options](https://github.com/lightningnetwork/lnd/blob/master/sample-lnd.conf)
* [connext config options](https://docs.connext.network/en/latest/quickstart/clientInstantiation.html#client-options)
* [opendexd config options](https://github.com/opendexnetwork/opendexd/blob/main/sample-opendex.conf)
