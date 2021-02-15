`opendex-cli` is the command line interface that handles much of the basic interaction with a running `opendexd` instance. If `opendexd` is installed globally, it can be launched from any directory. In the recommended [opendex-docker](/docs/liquidity-providers.md) setup, it can be used from within the `opendexd ctl` shell.

To get a list of up-to-date commands, run:
```
opendex-cli --help
```

Calling any one of the listed commands with the `--help` flag will output additional instructions for that particular command.

Examples for commands:
```bash
# Manually connect to another opendexd instance (has to be running the same network: simnet/testnet/mainnet)
$ opendex-cli connect 025fbfe0e92bf0e5e64500ed542d51f4f9d59111a2d3fa142e90567ec417c4a617@1.opendex.network:8885

# Places a new limit order BUYING 10 LTC for a price of 0.0079 BTC per LTC
$ opendex-cli buy 10 LTC/BTC 0.0079

# Places a new limit order SELLING 5 LTC for the best market price
$ opendex-cli sell 5 LTC/BTC market
```

By default, the CLI output is formatted and abbreviated. Append `-j` to any of the CLI calls to receive the full output in JSON format.
