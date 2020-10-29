## Testing Smart Contracts

You can test your Web3j app with [Web3j-Unit](../web3j_unit.md).

#### Usage

Add the gradle dependency.

```groovy
repositories {
  mavenCentral()
  jcenter()
}
implementation "org.web3j:core:4.5.11"
testCompile "org.web3j:web3j-unit:4.5.11"
```

Deploy your contract in the test.

```java
@EVMTest
public class GreeterTest {

    @Test
    public void greeterDeploys(Web3j web3j, TransactionManager transactionManager, ContractGasProvider gasProvider) {
        Greeter greeter = Greeter.deploy(web3j, transactionManager, gasProvider, "Hello EVM").send()
        String greeting = greeter.greet().send()
        assertEquals("Hello EVM", greeting)
    }

}
```

For more detailed information check the [testing](../web3j_unit.md) section

