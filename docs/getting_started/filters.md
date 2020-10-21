Filters
-------

Web3j functional-reactive nature makes it really simple to setup observers that notify subscribers of events taking place on the blockchain.

To receive all new blocks as they are added to the blockchain:

```java
Subscription subscription = web3j.blockFlowable(false).subscribe(block -> {
    ...
});
```

To receive all new transactions as they are added to the blockchain:

```java
Subscription subscription = web3j.transactionFlowable().subscribe(tx -> {
    ...
});
```

To receive all pending transactions as they are submitted to the network (i.e. before they have been grouped into a block together):

```java
Subscription subscription = web3j.pendingTransactionFlowable().subscribe(tx -> {
    ...
});
```

Or, if you'd rather replay all blocks to the most current, and be notified of new subsequent blocks being created:

```java
Subscription subscription = replayPastAndFutureBlocksFlowable(
        <startBlockNumber>, <fullTxObjects>)
        .subscribe(block -> {
            ...
});
```

There are a number of other transaction and block replay Flowables described in [Filters and Events](../filters_and_events.md).

Topic filters are also supported:

```java
EthFilter filter = new EthFilter(DefaultBlockParameterName.EARLIEST,
        DefaultBlockParameterName.LATEST, <contract-address>)
             .addSingleTopic(...)|.addOptionalTopics(..., ...)|...;
web3j.ethLogFlowable(filter).subscribe(log -> {
    ...
});
```

Subscriptions should always be cancelled when no longer required:

```Java
subscription.unsubscribe();
```

**Note:** filters are not supported on Infura.

For further information refer to [Filters and Events](../filters_and_events.md) and the [Web3jRx](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/protocol/rx/Web3jRx.java) interface.
