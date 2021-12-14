# Run Nervos Layer 2 (Godwoken) Testnet Node

## Individual Docker commands

Actions required:

1. Find and replace `0.0.0.0` with your IP address. It should be one the values from commands: `ip addr show | grep inet` (or `ifconfig | grep inet` if on Mac).

### 1. Postgres, Redis, Web3, Godwoken
```
cd docker
docker-compose up
```

**Test Raw Godwoken**

Requirements:
- Sync at least 30 blocks

```
curl http://0.0.0.0:8119 -X POST -H "Content-Type: application/json" -d '{"jsonrpc": "2.0", "method":"gw_get_transaction", "params": ["0x7589fb1689b53700cec9966e16b83714f3583ec30d6184a782d329649c40d37e"], "id": 1}'
```

Expected result
```
{"jsonrpc":"2.0","result":{"transaction":{"raw":{"from_id":"0x2","to_id":"0x5","nonce":"0x5","args":"0xffffff504f4c590040420f00000000000000000000000000000000000000000000000000000000000000000000000000a40000009623609d000000000000000000000000fe1cae6e56bbb423821c71c685edfcda39d41d15000000000000000000000000a2694d757143110690f93de88ac1e345cb2c276500000000000000000000000000000000000000000000000000000000000000600000000000000000000000000000000000000000000000000000000000000004d09de08a00000000000000000000000000000000000000000000000000000000"},"signature":"0xc772ed5473e1b5eb5ad55ae87dcfe9dadba15003ad3fc715d04ae29938b4f1d320aebaeac14c5946743d07a054733f62f64ad57024fbd3ff543ee463d8abf8af00","hash":"0x7589fb1689b53700cec9966e16b83714f3583ec30d6184a782d329649c40d37e"},"status":"committed"},"id":1}
```

### 2. Test everything

**Test 1**

```
curl http://0.0.0.0:8024 -X POST -H "Content-Type: application/json" -d '{"jsonrpc": "2.0", "method":"eth_blockNumber", "params": [], "id": 1}'
```

Expected result (example): 
```
{"jsonrpc":"2.0","id":1,"result":"0x430"}
```

**Test 2**

```
curl http://0.0.0.0:8024 -X POST -H "Content-Type: application/json" -d '{"jsonrpc": "2.0", "method":"eth_syncing", "params": [], "id": 1}'
```

Expected result (example): 
```
{"jsonrpc":"2.0","id":1,"result":{"startingBlock":"0x380","currentBlock":"0x380","highestBlock":"0x380"}}
```

**Test 3**

Requirements:
- Sync at least 30 blocks

```
curl http://0.0.0.0:8024 -X POST -H "Content-Type: application/json" -d '{"jsonrpc": "2.0", "method":"eth_getTransactionByHash", "params": ["0x7589fb1689b53700cec9966e16b83714f3583ec30d6184a782d329649c40d37e"], "id": 1}'
```

Expected result:

```
{"jsonrpc":"2.0","id":1,"result":{"hash":"0x7589fb1689b53700cec9966e16b83714f3583ec30d6184a782d329649c40d37e","blockHash":"0x3db5355a9146c0697c15c091d174b0d0f4b6e59c0beeff92f0def2e42389785d","blockNumber":"0x1e","transactionIndex":"0x0","from":"0x8069d75814b6886ef39a5b3b0e84c3601b033480","to":"0x1b9d4121479f1708addae44631bf174a6b00cd93","gas":"0xf4240","gasPrice":"0x0","input":"0x9623609d000000000000000000000000fe1cae6e56bbb423821c71c685edfcda39d41d15000000000000000000000000a2694d757143110690f93de88ac1e345cb2c276500000000000000000000000000000000000000000000000000000000000000600000000000000000000000000000000000000000000000000000000000000004d09de08a00000000000000000000000000000000000000000000000000000000","nonce":"0x5","value":"0x0","v":"0x0","r":"0xc772ed5473e1b5eb5ad55ae87dcfe9dadba15003ad3fc715d04ae29938b4f1d3","s":"0x20aebaeac14c5946743d07a054733f62f64ad57024fbd3ff543ee463d8abf8af"}}
```