# `polycli rpcfuzz`

> Auto-generated documentation.

## Table of Contents

- [Description](#description)
- [Usage](#usage)
- [Flags](#flags)
- [See Also](#see-also)

## Description

Continually run a variety of RPC calls and fuzzers.

```bash
polycli rpcfuzz http://localhost:8545 [flags]
```

## Usage

This command will run a series of RPC calls against a given JSON RPC endpoint. The idea is to be able to check for various features and function to see if the RPC generally conforms to typical geth standards for the RPC.

Some setup might be needed depending on how you're testing. We'll demonstrate with geth.

In order to quickly test this, you can run geth in dev mode.

```bash
$ geth --dev --dev.period 5 --http --http.addr localhost \
    --http.port 8545 \
    --http.api 'admin,debug,web3,eth,txpool,personal,clique,miner,net' \
    --verbosity 5 --rpc.gascap 50000000  --rpc.txfeecap 0 \
    --miner.gaslimit  10 --miner.gasprice 1 --gpo.blocks 1 \
    --gpo.percentile 1 --gpo.maxprice 10 --gpo.ignoreprice 2 \
    --dev.gaslimit 50000000
```

If we wanted to use erigon for testing, we could do something like this as well.

```bash
$ erigon --chain dev --dev.period 5 --http --http.addr localhost \
    --http.port 8545 \
    --http.api 'admin,debug,web3,eth,txpool,personal,clique,miner,net' \
    --verbosity 5 --rpc.gascap 50000000 \
    --miner.gaslimit  10 --gpo.blocks 1 \
    --gpo.percentile 1
```

Once your Eth client is running and the RPC is functional, you'll need to transfer some amount of ether to a known account that ca be used for testing.

```
$ cast send --from "$(cast rpc --rpc-url localhost:8545 eth_coinbase | jq -r '.')" \
    --rpc-url localhost:8545 --unlocked --value 100ether \
    0x85dA99c8a7C2C95964c8EfD687E95E632Fc533D6
```

Then we might want to deploy some test smart contracts. For the purposes of testing we'll our ERC20 contract.

```bash
$ cast send --from 0x85dA99c8a7C2C95964c8EfD687E95E632Fc533D6 \
    --private-key 0x42b6e34dc21598a807dc19d7784c71b2a7a01f6480dc6f58258f78e539f1a1fa \
    --rpc-url localhost:8545 --create \
    "$(cat ./contracts/ERC20.bin)"
```

Once this has been completed this will be the address of the contract: `0x6fda56c57b0acadb96ed5624ac500c0429d59429`.

```bash
$  docker run -v $PWD/contracts:/contracts ethereum/solc:stable --storage-layout /contracts/ERC20.sol
```

### Links

- https://ethereum.github.io/execution-apis/api-documentation/
- https://ethereum.org/en/developers/docs/apis/json-rpc/
- https://json-schema.org/
- https://www.liquid-technologies.com/online-json-to-schema-converter

## Flags

```bash
      --contract-address string   The address of a contract that can be used for testing (default "0x6fda56c57b0acadb96ed5624ac500c0429d59429")
      --csv                       Flag to indicate that output will be exported as a CSV.
      --export-path string        The directory export path of the output of the tests. Must pair this with either --json, --csv, --md, or --html
      --fuzz                      Flag to indicate whether to fuzz input or not.
      --fuzzn int                 Number of times to run the fuzzer per test. (default 100)
  -h, --help                      help for rpcfuzz
      --html                      Flag to indicate that output will be exported as a HTML.
      --json                      Flag to indicate that output will be exported as a JSON.
      --md                        Flag to indicate that output will be exported as a Markdown.
      --namespaces string         Comma separated list of rpc namespaces to test (default "eth,web3,net,debug")
      --private-key string        The hex encoded private key that we'll use to sending transactions (default "42b6e34dc21598a807dc19d7784c71b2a7a01f6480dc6f58258f78e539f1a1fa")
      --seed int                  A seed for generating random values within the fuzzer (default 123456)
```

The command also inherits flags from parent commands.

```bash
      --config string   config file (default is $HOME/.polygon-cli.yaml)
      --pretty-logs     Should logs be in pretty format or JSON (default true)
  -v, --verbosity int   0 - Silent
                        100 Fatal
                        200 Error
                        300 Warning
                        400 Info
                        500 Debug
                        600 Trace (default 400)
```

## See also

- [polycli](polycli.md) - A Swiss Army knife of blockchain tools.