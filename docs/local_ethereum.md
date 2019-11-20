Local in-process Ethereum
=========================

Usually when interacting with Ethereum you do so by connecting to an Ethereum node. This node can either be hosted externally, or it might be one you've started locally.

In either case, you need to connect to another process to make transactions, deploy contracts or interact with these contracts. This brings with it a few drawbacks:

* You need to manage this external dependency, either through connection configuration or through a separate local application

* Communication between your JVM, which runs your Web3j integrations, and this external Ethereum process breaks the flow and adds lag

While sometimes you do want this external dependency, for example when you are doing transactions on Ethereum mainnet or a testnet, it is often unwanted while doing local development.

The [Web3j EVM](https://github.com/web3j/web3j-evm) library solves this by providing an in-process EVM and associated ledger. This EVM runs within the same JVM as your code, freeing you from needing to make external connections. No other processes are needed as the EVM and the ledger are themselves coded in Java, and the Web3j EVM is simply used as a library within your code.

If you want to use this feature within your unit and integration tests, we would recommend you explore the [Web3j unit](https://github.com/web3j/web3j-unit) library as that provides a more complete testing experience.
