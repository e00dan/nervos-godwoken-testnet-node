# Nervos Godwoken Layer 2 Node Easy Deploy

This repo contains Docker Compose files to deploy a Godwoken testnet or mainnet node.

## Basic Requirements

### Software

- Docker
- Docker Compose
- Git
- Curl

### Ports

- 8119
- 8024

## Configuration

The default configuration can be used without modification. However, it is recommended that the RPC nodes for the CKB node and CKB Indexer node be updated. The `3.235.223.161` node is a temporary public node that will disappear in the future. It is much faster and reliable to use locally hosted nodes for both.

The node IPs can be configured in the `[rpc_client]` section of the following file:

- config/godwoken-readonly-config.toml

Changing the ports can be configured by editing the following:

- config/godwoken-readonly-config.toml
- config/web3.env
- docker-compose.yml

## Basic Usage

Enter the `testnet` or `mainnet` directory, then execute `docker-compose up` to launch.

For launching the testnet:

```sh
cd testnet
docker-compose up
```

For launching the mainnet:

```sh
cd mainnet
docker-compose up
```

Note: You cannot run both the testnet and mainnet at the same time because they will both try to use ports `8119` and `8024`. This must be reconfigred in order to run them both on the same machine.

## Testing

### 1. Get Transaction (Godwoken RPC; Testnet)

**Sync at least 30 blocks**, then execute the following command:

```sh
curl http://localhost:8119 -X POST -H "Content-Type: application/json" -d '{"jsonrpc": "2.0", "method":"gw_get_transaction", "params": ["0x7589fb1689b53700cec9966e16b83714f3583ec30d6184a782d329649c40d37e"], "id": 1}'
```

Expected result:

```txt
{"jsonrpc":"2.0","result":{"transaction":{"raw":{"from_id":"0x2","to_id":"0x5","nonce":"0x5","args":"0xffffff504f4c590040420f00000000000000000000000000000000000000000000000000000000000000000000000000a40000009623609d000000000000000000000000fe1cae6e56bbb423821c71c685edfcda39d41d15000000000000000000000000a2694d757143110690f93de88ac1e345cb2c276500000000000000000000000000000000000000000000000000000000000000600000000000000000000000000000000000000000000000000000000000000004d09de08a00000000000000000000000000000000000000000000000000000000"},"signature":"0xc772ed5473e1b5eb5ad55ae87dcfe9dadba15003ad3fc715d04ae29938b4f1d320aebaeac14c5946743d07a054733f62f64ad57024fbd3ff543ee463d8abf8af00","hash":"0x7589fb1689b53700cec9966e16b83714f3583ec30d6184a782d329649c40d37e"},"status":"committed"},"id":1}
```

### 2. Block Number

```sh
curl http://localhost:8024 -X POST -H "Content-Type: application/json" -d '{"jsonrpc": "2.0", "method":"eth_blockNumber", "params": [], "id": 1}'
```

Expected result (example):

```txt
{"jsonrpc":"2.0","id":1,"result":"0x430"}
```

### 3. Syncing

```sh
curl http://localhost:8024 -X POST -H "Content-Type: application/json" -d '{"jsonrpc": "2.0", "method":"eth_syncing", "params": [], "id": 1}'
```

Expected result (example):

```txt
{"jsonrpc":"2.0","id":1,"result":{"startingBlock":"0x380","currentBlock":"0x380","highestBlock":"0x380"}}
```

### 4. Get Transaction (Testnet)

Requirements:

- Sync at least 30 blocks

```sh
curl http://localhost:8024 -X POST -H "Content-Type: application/json" -d '{"jsonrpc": "2.0", "method":"eth_getTransactionByHash", "params": ["0x7589fb1689b53700cec9966e16b83714f3583ec30d6184a782d329649c40d37e"], "id": 1}'
```

Expected result:

```txt
{"jsonrpc":"2.0","id":1,"result":{"hash":"0x7589fb1689b53700cec9966e16b83714f3583ec30d6184a782d329649c40d37e","blockHash":"0x3db5355a9146c0697c15c091d174b0d0f4b6e59c0beeff92f0def2e42389785d","blockNumber":"0x1e","transactionIndex":"0x0","from":"0x8069d75814b6886ef39a5b3b0e84c3601b033480","to":"0x1b9d4121479f1708addae44631bf174a6b00cd93","gas":"0xf4240","gasPrice":"0x0","input":"0x9623609d000000000000000000000000fe1cae6e56bbb423821c71c685edfcda39d41d15000000000000000000000000a2694d757143110690f93de88ac1e345cb2c276500000000000000000000000000000000000000000000000000000000000000600000000000000000000000000000000000000000000000000000000000000004d09de08a00000000000000000000000000000000000000000000000000000000","nonce":"0x5","value":"0x0","v":"0x0","r":"0xc772ed5473e1b5eb5ad55ae87dcfe9dadba15003ad3fc715d04ae29938b4f1d3","s":"0x20aebaeac14c5946743d07a054733f62f64ad57024fbd3ff543ee463d8abf8af"}}
```
