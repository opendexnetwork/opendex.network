An *optional* configuration file uses [TOML](https://github.com/toml-lang/toml) and by default should be saved at  `~/.xud/xud.conf` on Linux or `AppData\Local\Xud\xud.conf` on Windows. Run `xud` at least once for this folder to be created. The xud repository contains an up-to-date [`sample-xud.conf`](https://github.com/ExchangeUnion/xud/blob/master/sample-xud.conf) which serves as template for creating `xud.conf`. It is possible to override the default data directory by launching xud with `xud --xudir=/path/to/custom/xudir`.

## Precedence
The precedence order in which configuration option values are applied is as follows (high to low):

1. Option given on the command line
2. Option read from the config file
3. Option default value (as seen in the output of `xud --help`)