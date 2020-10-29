Working with smart contracts and Java smart contract wrappers 
--------------------------------------------------------------

Web3j can auto-generate smart contract wrapper code to deploy and interact with smart contracts without leaving the JVM.

To generate the wrapper code, compile your smart contract:

``` bash
$ solc <contract>.sol --bin --abi --optimize -o <output-dir>/
```

Then generate the wrapper code using the [Epirus CLI](https://docs.epirus.io/sdk/cli/):

``` bash
epirus solidity generate -b /path/to/<smart-contract>.bin -a /path/to/<smart-contract>.abi -o /path/to/src/main/java -p com.your.organisation.name
```

Now you can create and deploy your smart contract:

```java
Web3j web3 = Web3j.build(new HttpService());  // defaults to http://localhost:8545/
Credentials credentials = WalletUtils.loadCredentials("password", "/path/to/walletfile");

YourSmartContract contract = YourSmartContract.deploy(
        <web3j>, <credentials>,
        GAS_PRICE, GAS_LIMIT,
        <param1>, ..., <paramN>).send();  // constructor params
```

Or use an existing contract:

```java
YourSmartContract contract = YourSmartContract.load(
        "0x<address>|<ensName>", <web3j>, <credentials>, GAS_PRICE, GAS_LIMIT);
```

To transact with a smart contract:

```java
TransactionReceipt transactionReceipt = contract.someMethod(
             <param1>,
             ...).send();
```

To call a smart contract:

```java
Type result = contract.someMethod(<param1>, ...).send();
```

For more information refer to [Solidity smart contract wrappers](../smart_contracts/construction_and_deployment.md#solidity-smart-contract-wrappers).


Smart contract examples
-----------------------

Web3j provides a number of smart contract examples in the project directory [codegen/src/test/resources/solidity](https://github.com/web3j/web3j/tree/master/codegen/src/test/resources/solidity)

It also provides integration tests for demonstrating the deploying and working with those smart contracts in the [integration-tests/src/test/java/org/web3j/protocol/scenarios](https://github.com/web3j/web3j/tree/master/integration-tests/src/test/java/org/web3j/protocol/scenarios) module.

![image](../img/smart_contract.png)

EIP-20 Ethereum token standard smart contract 
---------------------------------------------

There an Ethereum standard, [EIP-20](https://eips.ethereum.org/EIPS/eip-20) which started off as an [Ethereum Improvement Proposal
(EIP)](https://github.com/ethereum/EIPs), that defines the standard functions that a smart contract providing tokens should implement.

The EIP-20 standard provides function definitions, but does not provide an implementation example. However, there is an implementation provided in
[codegen/src/test/resources/solidity/contracts](https://github.com/web3j/web3j/tree/master/codegen/src/test/resources/solidity/contracts), which has been taken from ConsenSys' [Tokens project](https://github.com/ConsenSys/Tokens).

Open Zeppelin also provide an example implementation on [GitHub](https://github.com/OpenZeppelin/zeppelin-solidity/tree/master/contracts/token).

There are two integration tests that have been written to fully demonstrate the functionality of this token smart contract.

[HumanStandardTokenGeneratedIT](https://github.com/web3j/web3j/tree/master/integration-tests/src/test/java/org/web3j/protocol/scenarios/HumanStandardTokenGeneratedIT.java) uses the generated HumanStandardTokenGenerated [Solidity smart contract wrappers](../smart_contracts/construction_and_deployment.md#solidity-smart-contract-wrappers) to demonstrate this.

Alternatively, if you do not wish to use a smart contract wrapper and would like to work directly with the underlying JSON-RPC calls, please refer to [HumanStandardTokenIT](https://github.com/web3j/web3j/tree/master/integration-tests/src/test/java/org/web3j/protocol/scenarios/HumanStandardTokenIT.java).

