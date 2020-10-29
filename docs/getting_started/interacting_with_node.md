Start sending requests
----------------------

To send synchronous requests:

```java
Web3j web3 = Web3j.build(new HttpService());  // defaults to http://localhost:8545/
Web3ClientVersion web3ClientVersion = web3.web3ClientVersion().send();
String clientVersion = web3ClientVersion.getWeb3ClientVersion();
```

To send asynchronous requests using a CompletableFuture (Future on Android):

```java
Web3j web3 = Web3j.build(new HttpService());  // defaults to http://localhost:8545/
Web3ClientVersion web3ClientVersion = web3.web3ClientVersion().sendAsync().get();
String clientVersion = web3ClientVersion.getWeb3ClientVersion();
```

To use an RxJava Flowable:

```java
Web3j web3 = Web3j.build(new HttpService());  // defaults to http://localhost:8545/
web3.web3ClientVersion().flowable().subscribe(x -> {
    String clientVersion = x.getWeb3ClientVersion();
    ...
});
```

IPC
---

Web3j also supports fast inter-process communication (IPC) via file sockets to clients running on the same host as web3j. To connect simply use the relevant `IpcService` implementation instead of `HttpService`
when you create your service:

``` java
// OS X/Linux/Unix:
Web3j web3 = Web3j.build(new UnixIpcService("/path/to/socketfile"));
...

// Windows
Web3j web3 = Web3j.build(new WindowsIpcService("/path/to/namedpipefile"));
...
```

**Note:** IPC is not available on *web3j-android*.


Transactions
------------

Web3j provides support for both working with Ethereum wallet files (*recommended*) and Ethereum client admin commands for sending transactions.

## Send Ether

To send Ether to another party using your Ethereum wallet file:

```Java
Web3j web3 = Web3j.build(new HttpService());  // defaults to http://localhost:8545/
Credentials credentials = WalletUtils.loadCredentials("password", "/path/to/walletfile");
TransactionReceipt transactionReceipt = Transfer.sendFunds(
        web3, credentials, "0x<address>|<ensName>",
        BigDecimal.valueOf(1.0), Convert.Unit.ETHER)
        .send();
```

## Custom Transaction

Or if you wish to create your own custom transaction:

```Java
Web3j web3 = Web3j.build(new HttpService());  // defaults to http://localhost:8545/
Credentials credentials = WalletUtils.loadCredentials("password", "/path/to/walletfile");

// get the next available nonce
EthGetTransactionCount ethGetTransactionCount = web3j.ethGetTransactionCount(
             address, DefaultBlockParameterName.LATEST).send();
BigInteger nonce = ethGetTransactionCount.getTransactionCount();

// create our transaction
RawTransaction rawTransaction  = RawTransaction.createEtherTransaction(
             nonce, <gas price>, <gas limit>, <toAddress>, <value>);

// sign & send our transaction
byte[] signedMessage = TransactionEncoder.signMessage(rawTransaction, credentials);
String hexValue = Numeric.toHexString(signedMessage);
EthSendTransaction ethSendTransaction = web3j.ethSendRawTransaction(hexValue).send();
// ...
```

Although it's far simpler using Web3j's [Transfer](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/tx/Transfer.java) for transacting with Ether.



Using an Ethereum client's admin commands (make sure you have your wallet in the client's keystore):

```Java
Admin web3j = Admin.build(new HttpService());  // defaults to http://localhost:8545/
PersonalUnlockAccount personalUnlockAccount = web3j.personalUnlockAccount("0x000...", "a password").sendAsync().get();
if (personalUnlockAccount.accountUnlocked()) {
    // send a transaction
}
```

If you want to make use of OpenEthereum's [Personal](https://openethereum.github.io/wiki/JSONRPC-personal-module) 
or [Trace](https://openethereum.github.io/wiki/JSONRPC-trace-module), or 
Geth's [Personal](https://github.com/ethereum/go-ethereum/wiki/Management-APIs#personal) 
client APIs, you can use the `org.web3j:parity` and `org.web3j:geth` modules respectively.
