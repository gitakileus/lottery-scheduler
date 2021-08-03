# Pancake Lottery Scheduler

### Description

The scheduler is composed of multiple scripts used to call `startLottery`, `closeLottery`, and `drawFinalNumberAndMakeLotteryClaimable` functions, and trigger events; as well as monitoring lottery results.

### Configuration

- `Lottery`: Address of [PancakeSwapLottery](https://github.com/pancakeswap/pancake-contracts/tree/master/projects/lottery) contract
- `Ticket`: Ticket Price (denominated in USD) and Precision
- `Discount`: Divisor to compute discount magnitude for bulk ticket purchase
- `Rewards`: Reward breakdown per bracket (total must be equal to 10,000)
- `Treasury`: Fee (denominated as percentage) to 2 decimals (e.g.: 100 => 1%)

## Crontab

```shell script
# Close lottery
0 6,18 * * * cd /opt/pancake-lottery-scheduler && yarn execute:close:mainnet

# Draw lottery
3 6,18 * * * cd /opt/pancake-lottery-scheduler && yarn execute:draw:mainnet

# Start lottery
5 6,18 * * * cd /opt/pancake-lottery-scheduler && yarn execute:start:mainnet
```

### Deployment

Configuration can be overwritten by editing [config.ts](config.ts) file.

```shell script
# Export operator private key to be used on Hardhat Network Config.
$ export OPERATOR_PRIVATE_KEY=OPERATOR_PRIVATE_KEY

$ yarn execute:[command]:[network]
```

#### Command(s)

- `start`: Start a new lottery; for a given network.
- `close`: Close a lottery, based on `currentLotteryId`; for a given network.
- `draw`: Draw winning numbers, based on `currentLotteryId`; for a given network.

#### Network(s)

- `56` - Mainnet (`0x38`)

- `97` - Testnet (`0x61`)

### Logging

Logs will be generated at `logs/lottery-YYYY-MM-DD.log` and will be archived (.gz), with a daily rotation.

Example of logs for success:

```log
{"message":"[1970-01-01T06:05:00.000Z] network=testnet block=10000000 message='Started lottery' hash=0x... gasPrice=10.0 signer=0x...","level":"info"}
{"message":"[1970-01-01T18:00:00.000Z] network=testnet block=10010010 message='Closed lottery #123' hash=0x... gasPrice=10.0 signer=0x...","level":"info"}
{"message":"[1970-01-01T18:03:00.000Z] network=testnet block=10100100 message='Drawed lottery #123' hash=0x... gasPrice=10.0 signer=0x...","level":"info"}
{"message":"[1970-01-01T18:05:00.000Z] network=testnet block=11001000 message='Started lottery' hash=0x... gasPrice=10.0 signer=0x...","level":"info"}
```

Example of logs for error:

```log
{"message":"[1970-01-01T06:00:00.000Z] network=testnet message='Unsupported network'","level":"error"}
{"message":"[1970-01-01T18:00:00.000Z] network=testnet message='Invalid JSON RPC response'","level":"error"}
{"message":"[1970-01-02T06:00:00.000Z] network=testnet message='Invalid keyHash on RandomGenerator contract'","level":"error"}
```
