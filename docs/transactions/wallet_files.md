## Creating and working with wallet files 

In order to sign transactions offline, you need to have either your Ethereum wallet file or the public and private keys associated with an Ethereum wallet/account.

web3j is able to both generate a new secure Ethereum wallet file for you, or work with an existing wallet file.

To create a new wallet file:

```java
String fileName = WalletUtils.generateNewWalletFile(
        "your password",
        new File("/path/to/destination"));
```

To load the credentials from a wallet file:

```java
Credentials credentials = WalletUtils.loadCredentials(
        "your password",
        "/path/to/walletfile");
```

These credentials are then used to sign transactions.

Please refer to the [Web3 Secret Storage Definition](https://github.com/ethereum/wiki/wiki/Web3-Secret-Storage-Definition) for the full wallet file specification.

## Signing transactions

Transactions to be used in an offline signing capacity, should use the [RawTransaction](https://github.com/web3j/web3j/blob/master/crypto/src/main/java/org/web3j/crypto/RawTransaction.java) type for this purpose. The RawTransaction is similar to the previously mentioned Transaction type, however it does not require a _from_ address, as this can be inferred from the signature.

In order to create and sign a raw transaction, the sequence of events is as follows:

1.  Identify the next available [nonce](transaction_nonce.md) for the sender account
2.  Create the RawTransaction object
3.  Encode the RawTransaction object using [Recursive Length Prefix](https://docs.web3j.io/recursive_length_prefix/#recursive-length-prefix) encoding
4.  Sign the RawTransaction object
5.  Send the RawTransaction object to a node for processing

The nonce is an increasing numeric value which is used to uniquely identify transactions. A nonce can only be used once and until a transaction is mined, it is possible to send multiple versions of a transaction with the same nonce, however, once mined, any subsequent submissions will be rejected.

Once you have obtained the next available [nonce](transaction_nonce.md), the value can then be used to create your transaction object:

```java
RawTransaction rawTransaction  = RawTransaction.createEtherTransaction(
             nonce, <gas price>, <gas limit>, <toAddress>, <value>);
```

The transaction can then be signed and encoded:

```java
byte[] signedMessage = TransactionEncoder.signMessage(rawTransaction, <credentials>);
String hexValue = Numeric.toHexString(signedMessage);
```

Where the credentials are those loaded as per [Creating and working with wallet files](#creating-and-working-with-wallet-files)

The transaction is then sent using [eth_sendRawTransaction](https://eth.wiki/json-rpc/API):

```java
EthSendTransaction ethSendTransaction = web3j.ethSendRawTransaction(hexValue).sendAsync().get();
String transactionHash = ethSendTransaction.getTransactionHash();
// poll for transaction response via org.web3j.protocol.Web3j.ethGetTransactionReceipt(<txHash>)
```

Please refer to the integration test [CreateRawTransactionIT](https://github.com/web3j/web3j/blob/master/integration-tests/src/test/java/org/web3j/protocol/scenarios/CreateRawTransactionIT.java) for a full example of creating and sending a raw transaction.
