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

Use [EIP1559](https://eips.ethereum.org/EIPS/eip-1559) for sending Ether:

```java
Web3j web3 = Web3j.build(new HttpService());
Credentials credentials = WalletUtils.loadCredentials("password", "/path/to/walletfile");
TransactionReceipt transactionReceipt = Transfer.sendFundsEIP1559(
        web3, credentials, 
        "0x<address>|<ensName>", //toAddress
        BigDecimal.ONE.valueOf(1), //value
        Convert.Unit.ETHER, //unit
        BigInteger.valueOf(8_000_000), gasLimit
        DefaultGasProvider.GAS_LIMIT, //maxPriorityFeePerGas (max fee per gas transaction willing to give to miners)
        BigInteger.valueOf(3_100_000_000L) //maxFeePerGas (max fee transaction willing to pay)
        ).send();
```

Access new attributes effectiveGasPrice and type through TransactionReceipt:-

```java
transactionReceipt.getType();
 
transactionReceipt.effectiveGasPrice();
```

Use Web3j to get block and access new fields:-

```java
EthBlock.Block block = web3j.ethGetBlockByNumber(
    DefaultBlockParameter.valueOf(“<block number>”),
    true  //returnFullTransactionObjects
    ).send().get().getBlock();
 
 block.getBaseFeePerGas();
```

Also other options to get block or get uncle like:-

```java
EthBlock.Block block = web3j.ethGetBlockByHash(String blockHash, boolean returnFullTransactionObjects);
 
EthBlock.Block block = web3j.ethGetUncleByBlockHashAndIndex(String blockHash, BigInteger transactionIndex);
 
EthBlock.Block block = web3j.ethGetUncleByBlockNumberAndIndex(DefaultBlockParameter defaultBlockParameter, BigInteger transactionIndex);
```

Also access Transactions new field through Web3j 

```java
EthTransaction ethTransaction = web3j.ethGetTransactionByHash(String transactionHash).send();
 
EthTransaction ethTransaction = web3j.ethGetTransactionByBlockHashAndIndex
      (String blockHash, BigInteger transactionIndex).send();
EthTransaction ethTransaction = web3j.ethGetTransactionByBlockNumberAndIndex(
      DefaultBlockParameter defaultBlockParameter, BigInteger transactionIndex).send();
```