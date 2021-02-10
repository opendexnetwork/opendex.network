This page contains instructions to natively install `xud` and its minimal dependencies `lnd` (BTC) & `connext` (ETH & ERC20) on linux. It is mainly geared towards developers and administrators which prefer a native installation over docker. For all other users, we recommend our streamlined setup via [xud-docker](Market%20Maker%20Guide.md).

# Requirements

Make sure to have the following installed:
- [Node.js](https://nodejs.org/en/download/), active LTS (we recommend [installing via nvm](https://nodejs.org/en/download/package-manager/#nvm))
- [Go](https://golang.org/), v1.14 or higher
- a user called `xud`

# XUD

## Option 1: Installing latest release via npm
This is the easiest and fastest way to install xud on a `amd64` machine: 
```bash
sudo npm install xud -g --unsafe-perm
```

## Option 2: Cloning from GitHub

Testers and developers are encouraged to clone the repository from GitHub and install from source:

```bash
git clone https://github.com/ExchangeUnion/xud
cd xud
npm install
npm run compile
npm run compile:seedutil
```
If you are on an architecture that is *not* `amd64`, you'll have to remove `grpc-tools` and potentially others from the `devDependencies` section of [`package.json`](https://github.com/ExchangeUnion/xud/blob/master/package.json).

## Daemonize `xud`

If you want to daemonize `xud`, so that it starts on boot without needing its own terminal, you can do this using `systemd`:

```toml
[Unit]
Description=XUD

[Service]
User=xud
Group=xud
Type=simple
Environment=NODE_ENV=production
ExecStart=/home/xud/xud/bin/xud --mainnet
KillMode=process
KillSignal=SIGINT
```

# LND (BTC)

Follow the [lnd installation guide](https://github.com/lightningnetwork/lnd/blob/master/docs/INSTALL.md#installing-lnd).

## Daemonize `lnd`

If you want to daemonize `lnd`, so that it starts on boot without needing its own terminal, you can do this using `systemd`:

```toml
[Unit]
Description=LND

[Service]
User=xud
Group=xud
Type=simple
ExecStart=/home/xud/lnd/bin/lnd --bitcoin.mainnet
KillMode=process
KillSignal=SIGINT
```

# Connext

Follow the [docs](https://github.com/connext/vector#quick-start), you want the **`node`: vector node + database** stack.

## Daemonize `connext`

If you want to daemonize `connext`, so that it starts on boot without needing its own terminal, you can do this using `systemd`:

```toml
[Unit]
Description=Connext

[Service]
User=xud
Group=xud
Type=simple
Environment="NODE_ENV=production"
Environment="CONNEXT_NODE_URL=https://connext.boltz.exchange"
Environment="CONNEXT_ETH_PROVIDER_URL=http://eth.kilrau.com:41007"
Environment="LEGACY_MODE=true"
WorkingDirectory=/home/xud/connext/
ExecStart=node /home/xud/connext/build/src/index.js
KillMode=process
KillSignal=SIGINT
```

# Tor

You can install tor via `sudo apt install tor` on most linux distros nowadays, just make sure [the version is fairly recent](https://github.com/torproject/tor/releases). If not, consult the [tor installation guides](https://2019.www.torproject.org/docs/installguide.html.en). Run `systemctl status tor` or `ps aux | grep tor` to verify the tor process is running.

# Putting it all together

Create the following `xud.conf` in `/home/xud/.xud`:
```toml
mainnet = true

[p2p]
tor = true
torport = 9050

[connext]
disable = false
host = "localhost"
port = 5000
webhookhost = "localhost"
webhookport = 8887

[lnd.BTC]
disable = false
host = "localhost"
certpath = "/home/xud/.lnd/tls.cert"
macaroonpath = "/home/xud/.lnd/admin.macaroon"

[lnd.LTC]
disable = true
```

For convenience, consider adding `alias xucli='/home/xud/xud/bin/xucli -p 8886'` to the xud user's `.bashrc` and source it. Then restart `xud` once (e.g. with `systemctl restart xud`) and try running `xucli getinfo`, which should return with an overview of xud's, as well as lnd's and connext status.

Ping us in the help channel of our [Discord server](https://discord.gg/YgDhMSn) for support.

# Tips 'n Tricks

* When installing on a Raspberry Pi you might see `Unexpected error during initialization`. [Here](https://github.com/ExchangeUnion/xud/issues/1199#issuecomment-527819108) the solution.
* If you see an `install error` when installing via `npm install`, try `npm install --production` & `npm install typescript`.
