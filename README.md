# Arbiter (In Progress)

![](https://visitor-badge.laobi.icu/badge?page_id=arbiter)
![Github Actions](https://github.com/primitivefinance/arbiter/workflows/Rust/badge.svg)

> A stand-alone Rust program to events on UniswapV3 pools and...

## Motivation:

The current implementation abstracts away worrying about fetching and generating token bindings for any users. Arbitrage takes advantage of price discrepancies in the buy and sell prices of different markets for the same assets. Right now, searchers can detect differences in reported prices between markets and execute pure profit[^1] trades, which we call _atomic arbitrage_. The environment for arbitrage is competitive, but for less popular token pairs, there is room for new searchers to equilibrate the prices of different markets.

We want to develop a Rust program capable of detecting and executing arbitrage opportunities between exchanges. Our goal is to lower the barrier to entry for searching in hopes that it can help level the playing field for capturing arbitrage.

[^1]: 'Pure profit' refers to the fact that atomic arbitrage does not require arbitrageurs to put any capital at risk other than an upfront cost on building the strategy and gas cost to get their transaction included. This contributes to the lucrative nature of atomic arbitrage when done effectively

## Features:

#### TODOs:

- [x] Library of popular tokens includes symbols, addresses, and decimals.
- [x] Takes user input of token addresses and finds the corresponding PoolIDs for UniswapV3.
- [x] Monitors the UniswapV3 pool prices continuously.
- [x] Integrate with user-selected RPC endpoint.
- [x] Concurrent pool monitoring for multiple pools.
- [x] Data Monitoring Component: Clarivoyance.
- [x] Execution Component: Architect.
- [ ] Additional protocols.
  - [ ] Aave
  - [ ] Balancer
  - [ ] ...
- [ ] Announces when an arbitrage trade with profit exceeding the no-arbitrage bounds + gas cost is found between two exchanges.
- [ ] Computes Optimal Routing Algorithm.
- [ ] Executes atomic transactions between pools to...

## Build From Source

First, clone the repository to your local environment so

```
git clone https://github.com/primitivefinance/arbiter.git
cd arbiter
cargo install --path ./crates/cli
```

Set the PROVIDER environment variable to use a custom provider.
`arbiter` has many subcommands and therefore many arguments. To see the available arguments for pool monitoring, run the following:

```
arbiter see --help
```

This will display the `help` menu for the `Claivoyance` crate

```console
Access the `Clairvoyance` monitoring module via this subcommand

Usage: arbiter.exe see [TOKEN0] [TOKEN1] [BP] [FILE]

Arguments:
  [TOKEN0]  Token 0 of the pool [default: ETH]
  [TOKEN1]  Token 1 of the pool [default: USDC]
  [BP]      Basis point fee of the pool [default: 5]
  [FILE]    Sets a custom config file

Options:
  -h, --help  Print help information
```

In the above, `token0` and `token1` will be the token pair used to find the corresponding UniswapV3 pools. Upon running

```
arbiter see
```

we default to `token0=ETH`, `token1=USDC`, and `fee=5`. `arbiter` will return the [5BP pool](https://info.uniswap.org/#/pools/0x88e6a0c2ddd26feeb64f039a2c41296fcb3f5640) for the pair ETH/USDC on Uniswap. The program runs and streams transactions (swaps) that update the pool's price like so:

```console
------------NEW SWAP------------
From pool 0x88e6a0c2ddd26feeb64f039a2c41296fcb3f5640
Sender: 0x68b3465833fb72a70ecdf485e0e4c7bd8665fc45, Recipient: 0x1019bf2d607cc646a94a194f7a79e0b385065cff
amount_0 -5235133099
amount_1 4335000000000000000
liquidity 23260193077241608585
tick 205351
price "1.208239460504000000000000000000000000000e+3"
```

If you would like the price to be in terms of ETH rather than USD for a one basis point pool, you can run

```
arbiter see USDC ETH 30
```

Which will return the pool address and then log swaps on this pool with the price now denominated in ETH

```console
Uniswap Pool Result: 0x88e6a0c2ddd26feeb64f039a2c41296fcb3f5640
------------NEW SWAP------------
Pool:      0x88e6a0c2ddd26feeb64f039a2c41296fcb3f5640
Sender:    0x1111111254fb6c44bac0bed2854e76f90643097d
Recipient: 0x134603117a253dd4550eb1fc508e289761be9c3e
Amount_0:  -1949252708
Amount_1:  1603051840877282447
Liquidity: 21972098821216706277
Tick:      205282
Price:     "1.216568804789000000000000000000000000000e+3"
```

You may also build the executable with `cargo build`, which will output a binary in `target/`

## Setting Custom RPC

If you would like to use your own RPC endpoint, then you can set the environment variable `PROVIDER`. By default, the provider we have set is via Alchemy. To set your own environment variable on a UNIX OS just perform:

```
export PROVIDER=https://url-to-your-RPC-endpoint.xyz
```

and replace your own URL as needed. Double check the environment variable is set by:

```
echo $PROVIDER
```

or just list all environment variables with:

```
env
```

If you need to unset the `PROVIDER` variable, do:

```
unset PROVIDER
```

## Contributing

See our [Contributing Guidlines](https://github.com/primitivefinance/arbiter/blob/main/.github/CONTRIBUTING.md)
