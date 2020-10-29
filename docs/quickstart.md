Quickstart
-----------

- Use Epirus CLI (recommended for full project creation)
    - Install Epirus CLI (include Windows installation instructions too)
    - Run ```$ epirus new```
        - This will create a sample project to get you started. 
        It includes a `HelloWorld` smart contract and boilerplate code that is easy to follow.
    - Run app:
        - Create [Epirus account](https://docs.epirus.io/sdk/cli/#account-creation).
        - Provide parameters to run project locally.
            - eg: `epirus run <network>`

- Use [Maven](plugins/web3j_maven_plugin.md) / [Gradle](plugins/web3j_gradle_plugin.md) plugin.
    -  To add the Gradle plugin to your project:
        ```
            plugins {
               id "org.web3j" version "4.7.0"
            }
        ``` 
       
    - To add the Maven plugin to your project:
    
        ```
        <build>
            <plugins>
                <plugin>
                    <groupId>org.web3j</groupId>
                    <artifactId>web3j-maven-plugin</artifactId>
                    <version>4.6.5</version>
                    <configuration>
                        <soliditySourceFiles/>
                    </configuration>
                </plugin>
            </plugins>
        </build>
        ```
  
## Generating Java Wrappers for Solidity Contracts

To generate Java Wrappers for Solidity Contracts you need to add the `Web3j Gradle Plugin` to Gradle
```
plugins {
     id "org.web3j" version "4.7.0"
}
```

Once the plugin is downloaded you should see a new set of tasks under the web3j label:

![](./general_media/web3j_plugin.png)

The simplest way is to create a new folder called solidity in `src/main/solidity` and place your smart contracts there.
To generate the Java wrappers simply run generateContractWrappers or `./gradlew/generateContractWrappers`.
The wrappers will generate under `build/generated/source/web3j/main/java`. 

## Deploying a Smart Contract

To deploy the HelloWorld contract from the previous example:

```java
  Web3j web3j = Web3j.build(new HttpService("<your_node_url>"));
  HelloWorld helloWorld = HelloWorld.deploy(web3j, Credentials.create("your_private_key"), new DefaultGasProvider(), "Hello Blockchain World").send();
  String greeting = helloWorld.greeting().send();
  web3j.shutdown();
```

There are many ways to create `Credentials` in web3j, in the above example I decided to use a private key.

You can also use:

- `Credentials.create(ECKeyPair ecKeyPair)`
- `Credentials.create(String privateKey, String publicKey)`

You can also use the `WalletUtils` class to load your credentials from various formats:

- `WalletUtils.loadCredentials(String password,String source)`
- `WalletUtils.loadBip39Credentials(String password,String mnemonic)`
- `WalletUtils.loadBip39Credentials(String password,String mnemonic)`
- `WalletUtils.loadJsonCredentials(String password,String content)`

## Loading a Smart Contract 

If you have already deployed a contract and would like to interact with it through web3j then the Java Wrappers or your smart contract have a load method.

```java
 Web3j web3j = Web3j.build(new HttpService("<your_node_url>"));
 String greeting;
 HelloWorld helloWorld = HelloWorld.load("your_contract_address", web3j, Credentials.create("your_private_key"), new DefaultGasProvider());
 if (helloWorld.isValid()) {
    greeting = helloWorld.greeting().send();
 }
 web3j.shutdown();
```
It is important that the loaded contract is checked using the `isValid()` method. This method will return false if the contract's bytecode does not match with the deployed one.



## Picking Gas Providers

The public Interface `ContractGasProvider` has two implementations that can be used in order to pass in a gas provider to the deploy method of the contract.

The `DefaultGasProvider` is pre-defined implementation of the `ContractGasProvider` that has set values for gas price and gas limit.

- `GAS_LIMIT = BigInteger.valueOf(9_000_000);`
- `GAS_PRICE = BigInteger.valueOf(4_100_000_000L));`

If a more flexible approach is required then the `StaticGasProvider` can take custom gas price and gas limit values as BigIntegers.
    
