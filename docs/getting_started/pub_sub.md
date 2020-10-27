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

There are a number of other transaction and block replay Flowables described in [Filters and Events](../advanced/filters_and_events.md).

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

For further information refer to [Filters and Events](../advanced/filters_and_events.md) and the [Web3jRx](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/protocol/rx/Web3jRx.java) interface.


Publish/Subscribe
---------------------------

Ethereum clients implement the [pub/sub](https://geth.ethereum.org/docs/rpc/pubsub) mechanism that provides the capability to subscribe to events from the network, allowing these clients to take custom actions as needed. In doing so it alleviates the need to use polling and is more efficient. This is achieved by using the WebSocket protocol instead of HTTP protocol.

Pub/Sub methods are available via the _WebSocketService_ class, and allows the client to:

-   send an RPC call over WebSocket protocol
-   subscribe to WebSocket events
-   unsubscribe from a stream of events

To create an instance of the _WebSocketService_ class you need to first to create an instance of the _WebSocketClient_ that connects to an Ethereum client via WebSocket protocol, and then pass it to the _WebSocketService_ constructor:

```Java
final WebSocketClient webSocketClient = new WebSocketClient(new URI("ws://localhost/"));
final boolean includeRawResponses = false;
final WebSocketService webSocketService = new WebSocketService(webSocketClient, includeRawResponses)
```

To send an RPC request using the WebSocket protocol one need to use the _sendAsync_ method on the _WebSocketService_ instance:

```java
// Request to get a version of an Ethereum client
final Request<?, Web3ClientVersion> request = new Request<>(
     // Name of an RPC method to call
     "web3_clientVersion",
     // Parameters for the method. "web3_clientVersion" does not expect any
     Collections.<String>emptyList(),
     // Service that is used to send a request
     webSocketService,
     // Type of an RPC call to get an Ethereum client version
     Web3ClientVersion.class);

// Send an asynchronous request via WebSocket protocol
final CompletableFuture<Web3ClientVersion> reply = webSocketService.sendAsync(
                request,
                Web3ClientVersion.class);

// Get result of the reply
final Web3ClientVersion clientVersion = reply.get();
```

To use synchronous communication (i.e send a request and await a
response) one would need to use the _sync_ method instead:

```java
// Send a (synchronous) request via WebSocket protocol
final Web3ClientVersion clientVersion = webSocketService.send(
                request,
                Web3ClientVersion.class);
```

To subscribe to WebSocket events _WebSocketService_ provides the _subscribe_ method. _subscribe_ returns an
instance of the [Flowable](http://reactivex.io/RxJava/2.x/javadoc/io/reactivex/Flowable.html) interface from the RxJava library, which allows the processing of incoming events from an Ethereum network as a reactive stream.

To subscribe to a stream of events you should use _WebSocketService_ to send an RPC method via WebSocket; this is usually _eth_subscribe_. Events that it subscribes to depend on parameters to the _eth_subscribe_ method. You can find more in the [RPC documentation](https://github.com/ethereum/go-ethereum/wiki/RPC-PUB-SUB#supported-subscriptions):

```java
// A request to subscribe to a stream of events
final Request<?, EthSubscribe> subscribeRequest = new Request<>(
    // RPC method to subscribe to events
    "eth_subscribe",
    // Parameters that specify what events to subscribe to
    Arrays.asList("newHeads", Collections.emptyMap()),
    // Service that is used to send a request
    webSocketService,
    EthSubscribe.class);

final Flowable<NewHeadsNotification> events = webSocketService.subscribe(
     subscribeRequest,
     // RPC method that should be used to unsubscribe from events
     "eth_unsubscribe",
     // Type of events returned by a request
     NewHeadsNotification.class
);

// Subscribe to incoming events and process incoming events
final Disposable disposable = events.subscribe(event -> {
    // Process new heads event
});
```

Notice that we need to provide a name of a method to _WebSocketService_ that needs to be called to unsubscribe from a stream of events. This is because different Ethereum clients may have different methods to unsubscribe from particular events. For example, the OpenEthereum client requires use of the
_parity_unsubscribe_ method to unsubscribe from [pub/sub events](https://openethereum.github.io/wiki/JSONRPC-eth_pubsub-module).

To unsubscribe from a stream of events one needs to use a _Flowable_ instance for a particular events stream:

```java
final Flowable<NewHeadsNotification> events = ...
final Disposable disposable = events.subscribe(...)
disposable.dispose();
```

The methods described above are quite low-level, so we can use _Web3j_ implementation instead:

```java
final WebSocketService webSocketService = ...
final Web3j web3j = Web3j.build(webSocketService)
final Flowable<NewHeadsNotification> notifications = web3j.newHeadsNotifications()
```
