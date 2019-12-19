Web3j Unit
===========

A web3j-unit is an extension to [JUnit 5](https://junit.org/junit5/docs/current/user-guide/) which enables you to test solidity contracts like any other Java code.
Rather than having to spin up a node, or use Ganache for integration testing, Web3j-unit runs an EVM in process.
Web3j-unit also provides you with the flexibility of testing against specific Geth, Besu or Parity nodes.
A docker-compose network can also be spun up and used for testing contracts using web3j-unit.

# Usage

First, let's add the gradle dependency.

```groovy
repositories {
  mavenCentral()
  jcenter()
}
implementation "org.web3j:core:4.5.11"
testCompile "org.web3j:web3j-unit:4.5.11"
```


### Using EVMTest annotation

Create a new test with the `@EVMTest` annotation. An embedded EVM is used by default. To use Geth or Besu pass the node type into the annotation: `@EVMTest(NodeType.GETH)` or `@EVMTest(NodeType.BESU)`
```kotlin
@EVMTest
class GreeterTest {

}
```

Inject instance of `Web3j`, `TransactionManager` and `ContractGasProvider` in your test method.
```kotlin
@EVMTest
class GreeterTest {

    @Test
    fun greeterDeploys(
        web3j: Web3j,
        transactionManager: TransactionManager,
        gasProvider: ContractGasProvider
    ) {}

}
```

Deploy your contract in the test.
```kotlin
@EVMTest
class GreeterTest {

    @Test
    fun greeterDeploys(
        web3j: Web3j,
        transactionManager: TransactionManager,
        gasProvider: ContractGasProvider
    ) {
        val greeter = Greeter.deploy(web3j, transactionManager, gasProvider, "Hello EVM").send()
        val greeting = greeter.greet().send()
        assertEquals("Hello EVM", greeting)
    }

}
```

Run the test!

### Using EVMComposeTest annotation

Create a new test with the `@EVMComposeTest` annotation. 

By default, uses `test.yml` file in the project home, and runs web3j on service named `node1` exposing the port `8545`. 

Can be customised to use specific docker-compose file, service name and port by `@EVMComposeTest("src/test/resources/geth.yml", "ethnode1", 8080)` Here, we connect to the service named `ethnode1` in the `src/test/resources/geth.yml` docker-compose file which exposes the port `8080` for web3j to connect to.

```kotlin
@EVMComposeTest("src/test/resources/geth.yml", "ethnode1", 8080)
class GreeterTest {

}
```

Inject instance of `Web3j`, `TransactionManager` and `ContractGasProvider` in your test method.

```kotlin
@EVMComposeTest("src/test/resources/geth.yml", "ethnode1", 8080)
class GreeterTest {

    @Test
    fun greeterDeploys(
        web3j: Web3j,
        transactionManager: TransactionManager,
        gasProvider: ContractGasProvider
    ) {}

}
```

Deploy your contract in the test.

```kotlin
@EVMComposeTest("src/test/resources/geth.yml", "ethnode1", 8080)
class GreeterTest {

    @Test
    fun greeterDeploys(
        web3j: Web3j,
        transactionManager: TransactionManager,
        gasProvider: ContractGasProvider
    ) {
        val greeter = Greeter.deploy(web3j, transactionManager, gasProvider, "Hello EVM").send()
        val greeting = greeter.greet().send()
        assertEquals("Hello EVM", greeting)
    }

}
```

Run the test!

## Sample projects

Sample project using `@EVMTest` can be found [here](https://github.com/web3j/web3j-unitexample).

Sample project using `@EVMCompseTest` can be found [here](https://github.com/web3j/web3j-unit-docker-compose-example). This tests the Greeter contract using VMWare Concord nodes using a docker-compose file.

