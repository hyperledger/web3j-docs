## Recommended approach for working with smart contracts

When working with smart contract wrappers as outlined below, you will have to perform all of the conversions from Solidity to native Java types manually. It is far more effective to use web3j's
[Solidity smart contract wrappers](../smart_contracts/construction_and_deployment.md#solidity-smart-contract-wrappers) which take care of all code generation and this conversion for you.

## Creation of a smart contract

To deploy a new smart contract, the following attributes will need to be provided

`value`

 the amount of Ether you wish to deposit in the smart contract
 (assumes zero if not provided)

`data`

 the hex formatted, compiled smart contract creation code

```java
// using a raw transaction
RawTransaction rawTransaction = RawTransaction.createContractTransaction(
        <nonce>,
        <gasPrice>,
        <gasLimit>,
        <value>,
        "0x <compiled smart contract code>");
// send...

// get contract address
EthGetTransactionReceipt transactionReceipt =
             web3j.ethGetTransactionReceipt(transactionHash).send();

if (transactionReceipt.getTransactionReceipt.isPresent()) {
    String contractAddress = transactionReceipt.get().getContractAddress();
} else {
    // try again
}
```

If the smart contract contains a constructor, the associated constructor field values must be encoded and appended to the _compiled smart
contract code_:

```java
String encodedConstructor =
             FunctionEncoder.encodeConstructor(Arrays.asList(new Type(value), ...));

// using a regular transaction
Transaction transaction = Transaction.createContractTransaction(
        <fromAddress>,
        <nonce>,
        <gasPrice>,
        <gasLimit>,
        <value>,
        "0x <compiled smart contract code>" + encodedConstructor);

// send...
```

## Transacting with a smart contract

To transact with an existing smart contract, the following attributes will need to be provided:

`to`

 the smart contract address

`value`

 the amount of Ether you wish to deposit in the smart contract (if
 the smart contract accepts ether)

`data`

 the encoded function selector and parameter arguments

web3j takes care of the function encoding for you, for further details on the implementation refer to the [Application Binary Interface](../smart_contracts/application_binary_interface.md) section.

```java
Function function = new Function<>(
             "functionName",  // function we're calling
             Arrays.asList(new Type(value), ...),  // Parameters to pass as Solidity Types
             Arrays.asList(new TypeReference<Type>() {}, ...));

String encodedFunction = FunctionEncoder.encode(function)
Transaction transaction = Transaction.createFunctionCallTransaction(
             <from>, <gasPrice>, <gasLimit>, contractAddress, <funds>, encodedFunction);

org.web3j.protocol.core.methods.response.EthSendTransaction transactionResponse =
             web3j.ethSendTransaction(transaction).sendAsync().get();

String transactionHash = transactionResponse.getTransactionHash();

// wait for response using EthGetTransactionReceipt...
```

It is not possible to return values from transactional functional calls, regardless of the return type of the message signature. However, it is possible to capture values returned by functions using filters. Please
refer to the [Filters and Events](../advanced/filters_and_events.md) section for details.

## Querying the state of a smart contract 

This functionality is facilitated by the [eth_call](https://eth.wiki/json-rpc/API#eth_call) JSON-RPC call.

eth_call allows you to call a method on a smart contract to query a value. There is no transaction cost associated with this function, this is because it does not change the state of any smart contract method's
called, it simply returns the value from them:

```java
Function function = new Function<>(
             "functionName",
             Arrays.asList(new Type(value)),  // Solidity Types in smart contract functions
             Arrays.asList(new TypeReference<Type>() {}, ...));

String encodedFunction = FunctionEncoder.encode(function)
org.web3j.protocol.core.methods.response.EthCall response = web3j.ethCall(
             Transaction.createEthCallTransaction(<from>, contractAddress, encodedFunction),
             DefaultBlockParameterName.LATEST)
             .sendAsync().get();

List<Type> someTypes = FunctionReturnDecoder.decode(
             response.getValue(), function.getOutputParameters());
```

**Note:** If an invalid function call is made, or a null result is obtained, the return value will be an instance of
[Collections.emptyList()](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#emptyList--)