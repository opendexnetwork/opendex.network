An *optional* configuration file uses [TOML](https://github.com/toml-lang/toml) and by default should be saved at  `~/.opendexd/opendex.conf` on Linux or `AppData\Local\OpenDEX\opendex.conf` on Windows. Run `opendexd` at least once for this folder to be created. The `opendexd` repository contains an up-to-date [`sample-opendex.conf`](https://github.com/opendexnetwork/opendexd/blob/main/sample-opendex.conf) which serves as template for creating `opendex.conf`. It is possible to overwrite the default data directory by launching `opendexd` with `opendexd --opendexdir=/path/to/custom/opendexdir`.

## Precedence
The precedence order in which configuration option values are applied is as follows (high to low):

1. Option given on the command line
2. Option read from the config file
3. Option default value (as seen in the output of `opendexd --help`)