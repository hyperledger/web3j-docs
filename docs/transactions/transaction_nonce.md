## The transaction nonce 

The nonce is an increasing numeric value which is used to uniquely identify transactions. A nonce can only be used once and until a transaction is mined, it is possible to send multiple versions of a transaction with the same nonce, however, once mined, any subsequent submissions will be rejected.

You can obtain the next available nonce via the [eth_getTransactionCount](https://eth.wiki/json-rpc/API#eth_gettransactioncount) method:

```java
EthGetTransactionCount ethGetTransactionCount = web3j.ethGetTransactionCount(
             address, DefaultBlockParameterName.LATEST).sendAsync().get();

     BigInteger nonce = ethGetTransactionCount.getTransactionCount();
```

The nonce can then be used to create your transaction object:

```java
RawTransaction rawTransaction  = RawTransaction.createEtherTransaction(
             nonce, <gas price>, <gas limit>, <toAddress>, <value>);
```
