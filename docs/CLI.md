`xucli` is the command line interface that handles much of the basic interaction with a running `xud` instance. If `xud` is installed globally, it can be launched from any directory. In the recommended [xud-docker](User%20Guide.md) setup, it can be used from within the `xud ctl` shell.

To get a list of up-to-date commands, run:
```
xucli --help
```

Calling any one of the listed commands with the `--help` flag will output additional instructions for that particular command.

Examples for commands:
```bash
# Manually connect to another xud instance (has to be running the same network: simnet/testnet/mainnet)
$ xucli connect 025fbfe0e92bf0e5e64500ed542d51f4f9d59111a2d3fa142e90567ec417c4a617@xud1.exchangeunion.com:8885

# Places a new limit order BUYING 10 LTC for a price of 0.0079 BTC per LTC
$ xucli buy 10 LTC/BTC 0.0079

# Places a new limit order SELLING 5 LTC for the best market price
$ xucli sell 5 LTC/BTC market
```

By default, the CLI output is formatted and abbreviated. Append `-j` to any of the CLI calls to receive the full output in JSON format.
