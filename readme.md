# Run Nervos Layer 2 (Godwoken) Testnet Node

## Individual Docker commands

Actions required:

1. Find and replace `/home/kuzi/projects/nervos-testnet-node/` with absolute path on your system with this cloned repository.
2. Find and replace `172.31.61.119` with your IP address. For me it's one of the values from running: `ip addr show | grep inet`.

### 1. Postgres

Start:
```
docker run -p 5432:5432 -v /home/kuzi/projects/nervos-testnet-node/create_tables.sql:/docker-entrypoint-initdb.d/create_tables.sql -e POSTGRES_USER=user -e POSTGRES_DB=lumos -e POSTGRES_PASSWORD=password postgres:alpine
```

### 2. Web3

Start:
```
docker run -p 8024:8024 -v/home/kuzi/projects/nervos-testnet-node/web3.env:/godwoken-web3/packages/api-server/.env -w/godwoken-web3 nervos/godwoken-js-prebuilds:v0.7.3-rc4 yarn workspace @godwoken-web3/api-server start
```

### 3. Godwoken

Start:
```
docker run --rm -v/home/kuzi/projects/nervos-testnet-node/store.db:/store.db -v/home/kuzi/projects/nervos-testnet-node/testnet-godwoken-readonly-config.toml:/deploy/config.toml -v/home/kuzi/projects/nervos-testnet-node/block-producer-private-key.txt:/deploy/pk -p 8119:8119 -eRUST_BACKTRACE=1 nervos/godwoken-prebuilds:v0.6.4-rc1-hotfix.1 godwoken run -c /deploy/config.toml
```

**Test**

Requirements:
- Sync at least 30 blocks

```
curl http://0.0.0.0:8119 -X POST -H "Content-Type: application/json" -d '{"jsonrpc": "2.0", "method":"gw_get_transaction", "params": ["0x7589fb1689b53700cec9966e16b83714f3583ec30d6184a782d329649c40d37e"], "id": 1}'
```

Expected result
```
{"jsonrpc":"2.0","result":{"transaction":{"raw":{"from_id":"0x2","to_id":"0x5","nonce":"0x5","args":"0xffffff504f4c590040420f00000000000000000000000000000000000000000000000000000000000000000000000000a40000009623609d000000000000000000000000fe1cae6e56bbb423821c71c685edfcda39d41d15000000000000000000000000a2694d757143110690f93de88ac1e345cb2c276500000000000000000000000000000000000000000000000000000000000000600000000000000000000000000000000000000000000000000000000000000004d09de08a00000000000000000000000000000000000000000000000000000000"},"signature":"0xc772ed5473e1b5eb5ad55ae87dcfe9dadba15003ad3fc715d04ae29938b4f1d320aebaeac14c5946743d07a054733f62f64ad57024fbd3ff543ee463d8abf8af00","hash":"0x7589fb1689b53700cec9966e16b83714f3583ec30d6184a782d329649c40d37e"},"status":"committed"},"id":1}
```

### 4. Test everything

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