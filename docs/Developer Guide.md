This guide is intended to help developers who want to contribute to `xud`. Developers who want to build on top of `xud`, should check out the [API Documentation](https://api.exchangeunion.com).

## Contribution Guidelines

Be sure to read the [Contribution Guidelines](Contribute.md) for the repository before starting to work or opening a Pull Request.

## Recommended Development Environments

The following development environments are known to be compatible with `xud` and are recommended for developers that are unsure what tools to use.

### Visual Studio Code

[Visual Studio Code](https://code.visualstudio.com/) is a cross-platform code editor that's compatible with most popular programming languages and extensible via a large collection of plug-ins. 

#### Visual Studio Code Plugins

Consider using the following plugins for working with `xud`.

- [TSLint](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-tslint-plugin)
- [vscode-proto3](https://marketplace.visualstudio.com/items?itemName=zxh404.vscode-proto3)
- [Bracket Pair Colorizer](https://marketplace.visualstudio.com/items?itemName=coenraads.bracket-pair-colorizer) (definitely optional)

#### Visual Studio Code Environment Config

Adding the two files from [this gist](https://gist.github.com/sangaman/117af412eefc28c4f763c0152ddd3b99) into a `.vscode` folder within the folder where you've cloned `xud` will automatically provide debug configurations and general settings that are helpful when developing `xud`. 

## `xud` Setup

### Auto-restart `xud` on file change

Auto restart on every file change under `dist` folder with `nodemon`:

```
nodemon --watch dist -e js bin/xud
```

With some sample args disabling lndbtc/lndltc:

```
nodemon --watch dist -e js bin/xud --lndbtc.disable=true --lndltc.disable=true
```

### Connect `xud` to the `xud-simnet`

We recommend to connect the `xud` instance you are developing on with the `xud-simnet`. Combined with above restarting mechanism, this lets you instantly see how your changes behave in a real-world trading environment.

1. Create the following directory and config file to expose relevant ports for your `xud` to connect to: 
```bash
xud@ubuntu:~$ mkdir -p ~/.xud-docker/simnet/
xud@ubuntu:~$ nano ~/.xud-docker/simnet/simnet.conf
# add these lines to set LNDBTC and LNDLTC to use the Neutrino light client
[lndbtc]
expose-ports = ["30009:10009"]
[lndltc]
expose-ports = ["31009:10009"]
[connext]
expose-ports = ["25040:5040"]
# CTRL+S, CTRL+X.
```
2. Start the `xud-simnet` with default settings as described [here](Market%20Maker%20Guide.md).
3. Once `xud-simnet` is up and running, exit from the opened `xud ctl` session by typing `exit` or open a second terminal.
4. Stop the xud container with `docker stop simnet_xud_1`.
5. Copy the simnet lndbtc & lndltc folders into some path that you can easily access. E.g. into your home directory:
```
sudo cp -R ~/.xud-docker/simnet/data/lndbtc ~/
sudo cp -R ~/.xud-docker/simnet/data/lndltc ~/
```
6. Ensure that you own the copied folders:
```
sudo chown -R <youruser> ~/lndbtc
sudo chown -R <youruser> ~/lndltc
```
7. Create/change `xud.conf` in `~/.xud` to contain the following:
```bash
[lnd.BTC]
cltvdelta = 40
disable = false
host = "localhost"
nomacaroons = false
port = 30009
certpath = "/home/<youruser>/lndbtc/tls.cert"
macaroonpath = "/home/<youruser>/lndbtc/data/chain/bitcoin/simnet/admin.macaroon"
[lnd.LTC]
cltvdelta = 576
disable = false
host = "localhost"
nomacaroons = false
port = 31009
certpath = "/home/<youruser>/lndltc/tls.cert"
macaroonpath = "/home/<youruser>/lndltc/data/chain/litecoin/simnet/admin.macaroon"
[connext]
disable = false
host = "localhost"
port = 25040
```
8. Now you can start your local `xud` instance and it should connect to the running `xud-simnet` docker environment. You can check that everything works with `~/xud/bin$ ./xucli -p 28886 getinfo`. Ready to test your changes on `xud-simnet`!

## References

- [Official TypeScript Documentation](https://www.typescriptlang.org/docs/home.html)
- [Official Node.js Documentation](https://nodejs.org/en/docs/)
- [LND Developer Guide](https://dev.lightning.community/)
- [Official gRPC Documentation](https://grpc.io/docs/)
