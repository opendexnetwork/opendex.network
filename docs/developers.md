This guide is intended to help developers who want to contribute to `opendexd`. Developers who want to build services on top of `opendexd`, should check out the node's [API Documentation](https://api.opendex.network/).

## Contribution Guidelines

Be sure to read the [Contribution Guidelines](/docs/contribute.md) before starting to work or opening a Pull Request.

## Recommended Development Environments

The following development environments are known to be compatible with `opendexd` and are recommended for developers that are unsure what tools to use.

### Visual Studio Code

[Visual Studio Code](https://code.visualstudio.com/) is a cross-platform code editor that's compatible with most popular programming languages and extensible via a large collection of plug-ins. 

#### Visual Studio Code Plugins

Consider using the following plugins for working with `opendexd`.

- [TSLint](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-tslint-plugin)
- [vscode-proto3](https://marketplace.visualstudio.com/items?itemName=zxh404.vscode-proto3)
- [Bracket Pair Colorizer](https://marketplace.visualstudio.com/items?itemName=coenraads.bracket-pair-colorizer) (definitely optional)

#### Visual Studio Code Environment Config

Adding the two files from [this gist](https://gist.github.com/sangaman/117af412eefc28c4f763c0152ddd3b99) into a `.vscode` folder within the folder where you've cloned `opendexd` will automatically provide debug configurations and general settings that are helpful when developing `opendexd`. 

## `opendexd` Setup

### Auto-restart `opendexd` on file change

Auto restart on every file change under `dist` folder with `nodemon`:

```
nodemon --watch dist -e js bin/opendexd
```

With some sample args disabling lndbtc/lndltc:

```
nodemon --watch dist -e js bin/opendexd --lndbtc.disable=true --lndltc.disable=true
```

### Connect `opendexd` to testnet

We recommend to connect the `opendexd` instance you are developing on to testnet. Combined with above restarting mechanism, this lets you instantly see how your changes behave in a real-world trading environment.

1. Start testnet with default settings as described [here](/docs/liquidity-providers.md).
2. Once the environment is up and running, exit from the cli session by typing `exit` or open a second terminal.
3. Stop the opendexd container with `docker stop testnet_opendexd_1`.
4. Copy the testnet lndbtc & lndltc folders into some path that you can easily access. E.g. into your home directory:
```
sudo cp -R ~/.opendex-docker/testnet/data/lndbtc ~/
sudo cp -R ~/.opendex-docker/testnet/data/lndltc ~/
```
5. Ensure that you own the copied folders:
```
sudo chown -R <youruser> ~/lndbtc
sudo chown -R <youruser> ~/lndltc
```
7. Create/change `opendex.conf` in `~/.opendexd` to contain the following:
```bash
[lnd.BTC]
cltvdelta = 40
disable = false
host = "localhost"
nomacaroons = false
port = 20009
certpath = "/home/<youruser>/lndbtc/tls.cert"
macaroonpath = "/home/<youruser>/lndbtc/data/chain/bitcoin/testnet/admin.macaroon"
[lnd.LTC]
cltvdelta = 576
disable = false
host = "localhost"
nomacaroons = false
port = 21009
certpath = "/home/<youruser>/lndltc/tls.cert"
macaroonpath = "/home/<youruser>/lndltc/data/chain/litecoin/testnet/admin.macaroon"
[connext]
disable = false
host = "localhost"
port = 18000
```
8. Now you can start your local `opendexd` instance and it should connect to the running testnet docker environment. You can check that everything works with `~/opendexd/bin$ ./opendex-cli -p 18886 getinfo`.

## References

- [Official TypeScript Documentation](https://www.typescriptlang.org/docs/home.html)
- [Official Node.js Documentation](https://nodejs.org/en/docs/)
- [LND Developer Guide](https://dev.lightning.community/)
- [Official gRPC Documentation](https://grpc.io/docs/)
