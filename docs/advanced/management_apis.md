Management APIs
===============

In addition to implementing the standard [JSON-RPC](https://eth.wiki/json-rpc/API) API, Ethereum clients, such as [Geth](https://geth.ethereum.org/docs/rpc/server) and [OpenEthereum](https://openethereum.github.io/wiki/JSONRPC) provide additional management via JSON-RPC.

One of the key common pieces of functionality that they provide is the ability to create and unlock Ethereum accounts for transacting on the network. In Geth and OpenEthereum, this is implemented in their Personal modules, details of which are available below:

-   [OpenEthereum](https://openethereum.github.io/wiki/JSONRPC-personal-module)
-   [Geth](https://geth.ethereum.org/docs/rpc/ns-personal)

Support for the personal modules is available in web3j. Those methods that are common to both Geth and OpenEthereum reside in the [Admin](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/protocol/admin/Admin.java) module of web3j.

You can initialise a new web3j connector that supports this module using the factory method:

```java
Admin web3j = Admin.build(new HttpService());  // defaults to http://localhost:8545/
PersonalUnlockAccount personalUnlockAccount = admin.personalUnlockAccount("0x000...", "a password").send();
if (personalUnlockAccount.accountUnlocked()) {
    // send a transaction
}
```

For Geth specific methods, you can use the [Geth](https://github.com/web3j/web3j/blob/master/geth/src/main/java/org/web3j/protocol/geth/Geth.java) connector, and for OpenEthereum you can use the associated
[OpenEthereum](https://github.com/web3j/web3j/blob/master/parity/src/main/java/org/web3j/protocol/parity/Parity.java) connector. The `Parity` connector also provides support for OpenEthereum's [Trace](https://openethereum.github.io/wiki/JSONRPC-trace-module) module. These connectors are available in the web3j *geth* and *parity* modules respectively.

You can refer to the integration test [ParityIT](https://github.com/web3j/web3j/blob/master/integration-tests/src/test/java/org/web3j/protocol/parity/ParityIT.java) for further examples of working with these APIs.
