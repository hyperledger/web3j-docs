Construction and deployment
---------------------------

Construction and deployment of smart contracts happens with the *deploy* method:

```java
YourSmartContract contract = YourSmartContract.deploy(
        <web3j>, <credentials>, <contractGasProvider>,
        [<initialValue>,]
        <param1>, ..., <paramN>).send();
```

This will create a new instance of the smart contract on the Ethereum blockchain using the supplied credentials, and constructor parameter values.

The *<initialValue\>* parameter is only required if your smart contract accepts Ether on construction. This requires the Solidity [payable](https://solidity.readthedocs.io/en/v0.5.10/contracts.html#function-modifiers) modifier to be present in the contract.

It returns a new smart contract wrapper instance which contains the underlying address of the smart contract. If you wish to construct an instance of a smart contract wrapper with an existing smart contract,
simply pass in it's address:

```java
YourSmartContract contract = YourSmartContract.load(
        "0x<address>|<ensName>", web3j, credentials, contractGasProvider);
```

Deploying and interacting with smart contracts
----------------------------------------------

If you want to avoid the underlying implementation detail for working with smart contracts, Web3j provides [Solidity smart contract wrappers](#solidity-smart-contract-wrappers) which enable you to interact directly with all of a smart contract's methods via a generated wrapper object.

Alternatively, if you wish to send regular transactions or have more control over your interactions with your smart contracts, please refer to the sections [Solidity smart contract wrappers](#solidity-smart-contract-wrappers), [Transacting with a smart contract](../transactions/transactions_and_smart_contracts.md#Transacting with a smart contract) and [Querying the state of a smart contract](../transactions/transactions_and_smart_contracts.md#Querying the state of a smart contract ) for details.


Solidity smart contract wrappers
--------------------------------

Web3j supports the auto-generation of smart contract function wrappers in Java from Solidity ABI files.

The Web3j [Command Line Tools](http://docs.web3j.io/latest/command_line_tools/) tools ship with a command line utility for generating the smart contract function wrappers:

``` bash
$ web3j generate solidity [-hV] [-jt] [-st] -a=<abiFile> [-b=<binFile>] -o=<destinationFileDir> -p=<packageName>

   -h, --help                        Show this help message and exit.
   -V, --version                     Print version information and exit.
   -jt, --javaTypes                  use native java types. Default: true
   -st, --solidityTypes              use solidity types.
   -a, --abiFile=<abiFile>           abi file with contract definition.
   -b, --binFile=<binFile>           optional bin file with contract compiled code in order to generate deploy methods.
   -o, --outputDir=<destinationFileDir> destination base directory.
   -p, --package=<packageName>       base package name.
```

`<binfile>` is required for [Contract validity](contract_validity.md)

In versions prior to 3.x of Web3j, the generated smart contract wrappers used native Solidity types. From Web3j 3.x onwards, Java types are created by default. You can create Solidity types using the *--solidityTypes* command line argument.

From Web3j version 4.6.x onwards, the generator supports ABIv2 for contract compiled with Solidity compiler version 0.6.x. This means that you can have structs as input/output/event parameters in your smart contract.

You can also generate the wrappers by calling the Java class directly:

``` bash
org.web3j.codegen.SolidityFunctionWrapperGenerator -b /path/to/<smart-contract>.bin -a /path/to/<smart-contract>.abi -o /path/to/src/main/java -p com.your.organisation.name
```

Where the *bin* and *abi* are obtained as per [Compiling Solidity source code](compiling_solidity.md#compiling-solidity-source-code)

The native Java to Solidity type conversions used are detailed in the [Application Binary Interface](application_binary_interface.md) section.

The smart contract wrappers support all common operations for working with smart contracts:

-   [Construction and deployment](construction_and_deployment.md)
-   [Invoking transactions and events](interacting_with_smart_contract.md#invoking-transactions-and-events)
-   [Calling constant methods](interacting_with_smart_contract.md#calling-constant-methods)
-   [Contract validity](contract_validity.md)

Any method calls that requires an underlying JSON-RPC call to take place will return a Future to avoid blocking.

Web3j also supports the generation of Java smart contract function wrappers directly from [Truffle's](http://truffleframework.com/) [Contract
Schema](https://github.com/trufflesuite/truffle-contract-schema) via the [Command Line Tools](http://docs.web3j.io/latest/command_line_tools/#solidity-smart-contract-wrapper-generator) utility.

``` bash
$ web3j generate truffle [--javaTypes|--solidityTypes] /path/to/<truffle-smart-contract-output>.json -o /path/to/src/main/java -p com.your.organisation.name
```

And this also can be invoked by calling the Java class:

``` bash
org.web3j.codegen.TruffleJsonFunctionWrapperGenerator /path/to/<truffle-smart-contract-output>.json -o /path/to/src/main/java -p com.your.organisation.name
```

A wrapper generated this way is "enhanced" to expose the per-network deployed address of the contract. These addresses are from the truffle deployment at the time the wrapper is generated.

