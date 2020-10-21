Transaction Managers
--------------------

web3j provides a [TransactionManager](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/tx/TransactionManager.java) abstraction to control the manner you connect to Ethereum clients with. The default mechanism uses web3j's [RawTransactionManager](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/tx/RawTransactionManager.java) which works with Ethereum wallet files to sign transactions offline before submitting them to the network.

However, you may wish to modify the transaction manager, which you can pass to the smart contract deployment and creation methods instead of a credentials object, i.e.:

```java
YourSmartContract contract = YourSmartContract.deploy(
        <web3j>, <transactionManager>, GAS_PRICE, GAS_LIMIT,
        <param1>, ..., <paramN>).send();
```

In addition to the RawTransactionManager, web3j provides a [ClientTransactionManager](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/tx/ClientTransactionManager.java) which passes the responsibility of signing your transaction on to the Ethereum client you are connecting to.

There is also a [ReadonlyTransactionManager](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/tx/ReadonlyTransactionManager.java) for when you only want to retrieve data from a smart contract, but not transact with it.

Specifying the Chain Id on Transactions (EIP-155)
-------------------------------------------------

The RawTransactionManager takes an optional *chainId* parameter to specify the chain id to be used on transactions as per [EIP-155](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-155.md). This prevents transactions from one chain being re-broadcast onto another chain, such as from Ropsten to Mainnet:

```java
TransactionManager transactionManager = new RawTransactionManager(
        web3j, credentials, ChainId.MAINNET);
```

In order to avoid having to change config or code to specify which chain you are working with, web3j's default behaviour is to not specify chain ids on transactions to simplify working with the library. However, the recommendation of the Ethereum community is to use them.

You can obtain the chain id of the network that your Ethereum client is connected to with the following request:

```java
web3j.netVersion().send().getNetVersion();
```

Transaction Receipt Processors
------------------------------

By default, when a new transaction is submitted by web3j to an Ethereum client, web3j will continually poll the client until it receives a [TransactionReceipt](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/protocol/core/methods/response/TransactionReceipt.java), indicating that the transaction has been added to the blockchain. If you are sending a number of transactions asynchronously with web3j, this can result in a number of threads polling the client concurrently.

To reduce this polling overhead, web3j provides configurable [TransactionReceiptProcessors](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/tx/response/TransactionReceiptProcessor.java).

There are a number of processors provided in web3j:

-   [PollingTransactionReceiptProcessor](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/tx/response/PollingTransactionReceiptProcessor.java) is the default processor used in web3j, which polls periodically for a transaction receipt for each individual pending transaction.
-   [QueuingTransactionReceiptProcessor](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/tx/response/QueuingTransactionReceiptProcessor.java) has an internal queue of all pending transactions. It contains a
    worker that runs periodically to query if a transaction receipt is available yet. If a receipt is found, a callback to the client is invoked.
-   [NoOpProcessor](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/tx/response/NoOpProcessor.java) provides an [EmptyTransactionReceipt](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/tx/response/EmptyTransactionReceipt.java) to clients which only contains the transaction hash. This is for clients who do not want web3j to perform any polling for a transaction receipt.

**Note:** the
[EmptyTransactionReceipt](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/tx/response/EmptyTransactionReceipt.java) is also provided in the the initial response from the [QueuingTransactionReceiptProcessor](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/tx/response/QueuingTransactionReceiptProcessor.java). This allows the caller to have the transaction hash for the transaction
that was submitted to the network.

If you do not wish to use the default processor([PollingTransactionReceiptProcessor](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/tx/response/PollingTransactionReceiptProcessor.java)), you can specify the transaction receipt processor to use as follows:

```java
TransactionReceiptProcessor transactionReceiptProcessor =
        new QueuingTransactionReceiptProcessor(web3j, new Callback() {
                 @Override
                 public void accept(TransactionReceipt transactionReceipt) {
                     // process transactionReceipt
                 }

                 @Override
                 public void exception(Exception exception) {
                     // handle exception
                 }
TransactionManager transactionManager = new RawTransactionManager(
        web3j, credentials, ChainId.MAINNET, transactionReceiptProcessor);
```

If you require further information, the [FastRawTransactionManagerIT](https://github.com/web3j/web3j/blob/master/integration-tests/src/test/java/org/web3j/protocol/scenarios/FastRawTransactionManagerIT.java) demonstrates the polling and queuing approaches.

Invoking transactions and events 
--------------------------------

All transactional smart contract methods are named identically to their Solidity methods, taking the same parameter values. Transactional calls do not return any values, regardless of the return type specified on the method. Hence, for all transactional methods the [Transaction Receipt](https://eth.wiki/json-rpc/API#eth_gettransactionreceipt) associated with the transaction is returned.:

```java
TransactionReceipt transactionReceipt = contract.someMethod(
             <param1>,
             ...).send();
```

The transaction receipt is useful for two reasons:

1.  It provides details of the mined block that the transaction resides in
2.  [Solidity events](http://Solidity.readthedocs.io/en/develop/contracts.html?highlight=events#events) that are called will be logged as part of the transaction, which can then be extracted

Any events defined within a smart contract will be represented in the smart contract wrapper with a method named *process<Event Name\>Event*, which takes the Transaction Receipt and from this extracts the indexed and non-indexed event parameters, which are returned decoded in an
instance of the [EventValues](https://github.com/web3j/web3j/blob/master/abi/src/main/java/org/web3j/abi/EventValues.java) object.:

```java
EventValues eventValues = contract.processSomeEvent(transactionReceipt);
```

Alternatively you can use an Flowable filter instead which will listen for events associated with the smart contract:

```java
contract.someEventFlowable(startBlock, endBlock).
        .subscribe(event -> ...);
```

For more information on working with Flowable filters, refer to [Filters and Events](filters_and_events.md).

**Remember** that for any indexed array, bytes and string Solidity parameter types, a Keccak-256 hash of their values will be returned, see the [documentation](http://Solidity.readthedocs.io/en/latest/contracts.html#events) for further information.

Calling constant methods
------------------------

Constant methods are those that read a value in a smart contract, and do not alter the state of the smart contract. These methods are available with the same method signature as the smart contract they were generated from:

```java
Type result = contract.someMethod(<param1>, ...).send();
```

Dynamic gas price and limit 
---------------------------

When working with smart contracts you may want to specify different gas price and limit values depending on the function being invoked. You can do that by creating your own [ContractGasProvider](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/tx/gas/ContractGasProvider.java) for the smart contract wrapper.

Every generated wrapper contains all smart contract method names listed as a constants, which facilitates compilation-time matching via a *switch* statement.

For example, using the [Greeter](https://github.com/web3j/web3j/blob/master/codegen/src/test/resources/solidity/greeter/Greeter.sol) contract:

```java
Greeter greeter = new Greeter(...);
greeter.setGasProvider(new DefaultGasProvider() {
    @Override
    public BigInteger getGasPrice(String contractFunc) {
        switch (contractFunc) {
            case Greeter.FUNC_GREET: return BigInteger.valueOf(22_000_000_000L);
            case Greeter.FUNC_KILL: return BigInteger.valueOf(44_000_000_000L);
            default: throw new NotImplementedException();
        }
    }

    @Override
    public BigInteger getGasLimit(String contractFunc) {
        switch (contractFunc) {
            case Greeter.FUNC_GREET: return BigInteger.valueOf(4_300_000);
            case Greeter.FUNC_KILL: return BigInteger.valueOf(5_300_000);
            default: throw new NotImplementedException();
        }
    }
});
```
