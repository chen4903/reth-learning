# EVM Execution Clients

## Brief

- JSON-RPC transports support:

  - HTTP
  - WebSocket
  - Unix Domain Sockets(IPC)

- JSON-RPC Namespaces

  - You can find all the standard Methods [here](https://ethereum.github.io/execution-apis/api-documentation/). It lists the RPCs that client should implement at least.  Only three Namespace: debug, engine, eth. You can find the all apis [here](https://github.com/ethereum/execution-apis/tree/main/src).

    > `engine` is used for beacon client for the target of consensus mechanism. We do not need to call it. The beacon client will visit the execution client and call the `engine_method` RPC.

  - Some tips

    - `eth_call`: Executes a new message call immediately without creating a transaction on the block chain, which means that calling the view/pure function. Although `eth_call` is an only-read  request, it has a gas limit `50_000_000`.
    - `send_sendRawTransacion`: Submits a raw transaction.

  - Reth also implements other [Namespaces](https://paradigmxyz.github.io/reth/jsonrpc/intro.html).

```
[namespace]_[method]
```

















