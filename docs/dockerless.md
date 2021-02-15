This page contains instructions how to natively install `opendexd` and its minimal dependencies `lnd` (BTC) & `connext` (ETH & ERC20) on linux. It is mainly geared towards developers and administrators which prefer a native installation over docker.

# Requirements

Make sure to have the following installed:
- [Node.js](https://nodejs.org/en/download/), current active LTS (we recommend [installing via nvm](https://nodejs.org/en/download/package-manager/#nvm))
- [Go](https://golang.org/), v1.14 or higher
- a user called `opendexd`

# opendexd

## Option 1: Installing latest release via npm
This is the easiest and fastest way to install `opendexd` on a `amd64` machine: 
```bash
sudo npm install opendexd -g --unsafe-perm
```

## Option 2: Cloning from GitHub

Testers and developers are encouraged to clone the repository from GitHub and install from source:

```bash
git clone https://github.com/opendexnetwork/opendexd
cd opendexd
npm install
npm run compile
npm run compile:seedutil
```
If you are on an architecture that is *not* `amd64`, you'll have to remove `grpc-tools` and potentially others from the `devDependencies` section of [`package.json`](https://github.com/opendexnetwork/opendexd/blob/main/package.json).

## Daemonize `opendexd`

If you want to daemonize `opendexd`, so that it starts on boot without needing its own terminal, you can do this using `systemd`:

```toml
[Unit]
Description=opendexd

[Service]
User=opendexd
Group=opendexd
Type=simple
Environment=NODE_ENV=production
ExecStart=/home/opendexd/opendexd/bin/opendexd --mainnet
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
User=opendexd
Group=opendexd
Type=simple
ExecStart=/home/opendexd/lnd/bin/lnd --bitcoin.mainnet
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
User=opendexd
Group=opendexd
Type=simple
Environment="NODE_ENV=production"
Environment="CONNEXT_NODE_URL=https://connext.boltz.exchange"
Environment="CONNEXT_ETH_PROVIDER_URL=http://eth.kilrau.com:41007"
Environment="LEGACY_MODE=true"
WorkingDirectory=/home/opendexd/connext/
ExecStart=node /home/opendexd/connext/build/src/index.js
KillMode=process
KillSignal=SIGINT
```

# Tor

You can install tor via `sudo apt install tor` on most linux distros nowadays, just make sure [the version is fairly recent](https://github.com/torproject/tor/releases). If not, consult the [tor installation guides](https://2019.www.torproject.org/docs/installguide.html.en). Run `systemctl status tor` or `ps aux | grep tor` to verify the tor process is running.

# Putting it all together

Create the following `opendexd.conf` in `/home/opendexd/.opendexd`:
```toml
mainnet = true

[p2p]
tor = true
torport = 9050

[connext]
disable = false
host = "localhost"
port = 8000
webhookhost = "localhost"
webhookport = 8887

[lnd.BTC]
disable = false
host = "localhost"
certpath = "/home/opendexd/.lnd/tls.cert"
macaroonpath = "/home/opendexd/.lnd/admin.macaroon"

[lnd.LTC]
disable = true
```

For convenience, consider adding `alias opendex-cli='/home/opendexd/opendexd/bin/opendex-cli -p 8886'` to the opendexd user's `.bashrc` and source it. Then restart `opendexd` once (e.g. with `systemctl restart opendexd`) and try running `opendex-cli getinfo`, which should return with an overview of opendexd's, as well as lnd's and connext status.

Ping us in the help channel of our [Discord server](https://discord.gg/RnXFHpn) for support.

# Tips 'n Tricks

* When installing on a Raspberry Pi you might see `Unexpected error during initialization`. [Here](https://github.com/ExchangeUnion/xud/issues/1199#issuecomment-527819108) is the solution.
* If you see an `install error` when installing via `npm install`, try `npm install --production` & `npm install typescript`.
