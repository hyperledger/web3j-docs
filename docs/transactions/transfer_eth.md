## Transfer of Ether from one party to another

The sending of Ether between two parties requires a minimal number of details of the transaction object:

`to`

 the destination wallet address

`value`

  the amount of Ether you wish to send to the destination address

```java
BigInteger value = Convert.toWei("1.0", Convert.Unit.ETHER).toBigInteger();
RawTransaction rawTransaction  = RawTransaction.createEtherTransaction(
             <nonce>, <gas price>, <gas limit>, <toAddress>, value);
// send...
```

However, it is recommended that you use the [Transfer class](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/tx/Transfer.java) for sending Ether, which takes care of the nonce management and polling for a response for you:

```java
Web3j web3 = Web3j.build(new HttpService());  // defaults to http://localhost:8545/
Credentials credentials = WalletUtils.loadCredentials("password", "/path/to/walletfile");
TransactionReceipt transactionReceipt = Transfer.sendFunds(
        web3, credentials, "0x<address>|<ensName>",
        BigDecimal.valueOf(1.0), Convert.Unit.ETHER).send();
```
