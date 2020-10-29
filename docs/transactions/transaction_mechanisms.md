## Transaction mechanisms

When you have a valid account created with some Ether, there are two mechanisms you can use to transact with Ethereum.

1.  [Transaction signing via an Ethereum client](#transaction-signing-via-an-ethereum-client)
2.  [Offline transaction signing](#offline-transaction-signing)

Both mechanisms are supported via web3j.

## Transaction signing via an Ethereum client

In order to transact via an Ethereum client, you first need to ensure that the client you're transacting with knows about your wallet address. You are best off running your own Ethereum client such as Geth/Besu/OpenEthereum in order to do this. Once you have a client running, you can
create a wallet via:

- The [Geth Wiki](https://geth.ethereum.org/docs/interface/managing-your-accounts) contains a good run down of the different mechanisms Geth supports such as importing private key files, and creating a new account via it's console
- Alternatively you can use a JSON-RPC admin command for your client, such as _personal_newAccount_ for
  [OpenEthereum](https://openethereum.github.io/wiki/JSONRPC-personal-module#personal_newaccount) (This needs to be changed) or [Geth](https://geth.ethereum.org/docs/interface/managing-your-accounts)

With your wallet file created, you can unlock your account via web3j by first of all creating an instance of web3j that supports Geth/Besu/OpenEthereum admin commands:

```java
Admin web3j = Admin.build(new HttpService());
```

Then you can unlock the account, and providing this was successful, send a transaction:

```java
PersonalUnlockAccount personalUnlockAccount = web3j.personalUnlockAccount("0x000...", "a password").send();
if (personalUnlockAccount.accountUnlocked()) {
    // send a transaction
}
```

Transactions for sending in this manner should be created via [EthSendTransaction](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/protocol/core/methods/response/EthSendTransaction.java),
with the [Transaction](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/protocol/core/methods/request/Transaction.java) type:

```java
Transaction transaction = Transaction.createContractTransaction(
              <from address>,
              <nonce>,
              BigInteger.valueOf(<gas price>),  // we use default gas limit
              "0x...<smart contract code to execute>"
      );

      org.web3j.protocol.core.methods.response.EthSendTransaction
              transactionResponse = parity.ethSendTransaction(ethSendTransaction)
              .send();

      String transactionHash = transactionResponse.getTransactionHash();

      // poll for transaction response via org.web3j.protocol.Web3j.ethGetTransactionReceipt(<txHash>)
```

Where the _<nonce\>_ value is obtained as per [below](transaction_nonce.md)

Please refer to the integration test [DeployContractIT](https://github.com/web3j/web3j/blob/master/integration-tests/src/test/java/org/web3j/protocol/scenarios/DeployContractIT.java) and its superclass [Scenario](https://github.com/web3j/web3j/blob/master/integration-tests/src/test/java/org/web3j/protocol/scenarios/Scenario.java) for further details of this transaction workflow.

Further details of working with the different admin commands supported by web3j are available in the section
[Management APIs](../advanced/management_apis.md).

## Offline transaction signing

If you'd prefer not to manage your own Ethereum client, or do not want to provide wallet details such as your password to an Ethereum client, then offline transaction signing is the way to go.

Offline transaction signing allows you to sign a transaction using your Ethereum Ethereum wallet within web3j, allowing you to have complete control over your private credentials. A transaction created offline can then be sent to any Ethereum client on the network, which will propagate the transaction out to other nodes, provided it is a valid transaction.

You can also perform out of process transaction signing if required. This can be achieved by overriding the _sign_ method in [ECKeyPair](https://github.com/web3j/web3j/blob/master/crypto/src/main/java/org/web3j/crypto/ECKeyPair.java#L41).
