Web3j Core Modules
=======

To provide greater flexibility for developers wishing to work with web3j, the project is made up of a number of modules.

In dependency order, they are as follows:

-   utils - Minimal set of utility classes
-   rlp - Recursive Length Prefix (RLP) encoders
-   abi - Application Binary Interface (ABI) encoders
-   crypto - cryptographic library for transaction signing and key/wallet management in Ethereum
-   tuples - Simple tuples library
-   core - Much like the previous web3j core artifact without the code generators
-   codegen - code generators
-   console - command-line tools

The below modules only depend on the core module.

-   geth - Geth specific JSON-RPC module
-   parity - OpenEthereum specific JSON-RPC module
-   infura - Infura specific HTTP header support
-   contracts - Support for specific EIP's (Ethereum Improvement Proposals)
-   besu - Support for private transactions on Hyperledger Besu

For most use cases (interacting with the network and smart contracts) the *core* module should be all you need. The dependencies of the core module are very granular and only likely to be of use if your project is
focussed on a very specific interaction with the Ethereum network (such as ABI/RLP encoding, transaction signing but not submission, etc).

All modules are published to both Maven Central and Bintray, with the published artifact names using the names listed above, i.e.:

For Java:

   `org.web3j: <module-name\> : <version\>`

For Android:

   `org.web3j: <module-name\> : <version\>-android`

Further details
---------------

In the Java  build:

-   Web3j provides type safe access to all responses. Optional or null responses are wrapped in Java's [Optional](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html) type.
-   Asynchronous requests are wrapped in a Java [CompletableFutures](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html). Web3j provides a wrapper around all async requests to ensure that any exceptions during execution will be captured rather then silently discarded. This is due to the lack of support in *CompletableFutures* for checked exceptions, which are often rethrown as unchecked exception causing problems with detection. See the [Async.run()](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/utils/Async.java) and its associated [test](https://github.com/web3j/web3j/blob/master/core/src/test/java/org/web3j/utils/AsyncTest.java) for details.

In both the Java and Android builds:

-   Quantity payload types are returned as [BigIntegers](https://docs.oracle.com/javase/8/docs/api/java/math/BigInteger.html). For simple results, you can obtain the quantity as a String via [Response](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/protocol/core/Response.java).getResult().
-   It's also possible to include the raw JSON payload in responses via the *includeRawResponse* parameter, present in the [HttpService](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/protocol/http/HttpService.java) and [IpcService](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/protocol/ipc/IpcService.java) classes.
