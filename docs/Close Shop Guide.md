This guide is written for anyone looking to "close shop", to withdraw all funds from an xud environment.

# Close Shop

Enter your environment via:
```bash
bash ~/xud.sh
1) Simnet
2) Testnet
3) Mainnet
Please choose the network: 3
```

Stop arby to prevent it from issuing orders:
```bash
stop arby
```

Close all BTC & LTC channels:
```bash
lndbtc-lncli closeallchannels
lndltc-lncli closeallchannels
```

Check your ETH/ERC20 channel balances with `getbalance` and close channels using the full channel balance in the following command:
```bash
closechannel ETH --amount 0.5234
closechannel USDT --amount 124.12
```

`getbalance` should now show all **channel** balances as `0`. Once your ETH/ERC20 channel balances are available as `Wallet Balance (Not Tradable)`, you can import your seed/private key into a wallet like metamask. To do this, run the following command and follow the instructions:
```bash
getethmnemonic
```

Once your BTC and LTC channel balances are available as `Wallet Balance (Not Tradable)`, run:
```bash
lndbtc-lncli sendcoins --sweepall --addr <YOUR_EXTERNAL_BTC_ADDRESS>
lndltc-lncli sendcoins --sweepall --addr <YOUR_EXTERNAL_LTC_ADDRESS>
```

One more check that all balances are indeed `0` with `getbalance` and you can safely `down` and delete your environment.
