## Install Web3j

To start using Web3j you have two options:

### Use Epirus CLI

This is the recommended option for full project creation.
    
#### Install Epirus CLI

To install the command line tools you can follow these [instruction](https://docs.epirus.io/sdk/cli/#installation).

#### Create a new project     

`$ epirus new `

This will create a sample project to get you started. 

It includes a `HelloWorld` smart contract and boilerplate code that is easy to follow.

#### Run your project

If you don't have an Epirus account yet, you can create one [here](https://docs.epirus.io/sdk/cli/#account-creation).
Then run the project with the command `epirus run <network>` where network is `rinkeby` or `ropsten`.
You can also provide [custom parameters](https://docs.epirus.io/sdk/cli/#running-your-application-without-an-epirus-account) to run your project locally.

### Use Web3j plugins

To generate Java Wrappers for Solidity Contracts in your project, you need to add one of the Web3j's plugins available for Maven and Gradle.

#### Gradle plugin
    
To add the [Gradle](plugins/web3j_gradle_plugin.md) plugin to your project:

``` 
plugins {
    id "org.web3j" version "4.7.0"
}
``` 

Once the plugin is downloaded you should see a new set of tasks under the Web3j label:

![](./general_media/web3j_plugin.png)

The simplest way is to create a new folder `src/main/solidity` and place your smart contracts there.
To generate the Java wrappers simply run generateContractWrappers or `./gradlew/generateContractWrappers`.
The wrappers will generate under `build/generated/source/web3j/main/java`. 

#### Maven plugin        

To add the [Maven](plugins/web3j_maven_plugin.md) plugin to your project:
    
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
  
## Use Web3j

### Deploying a Smart Contract

To deploy the HelloWorld contract from the previous example:

```java
  Web3j web3j = Web3j.build(new HttpService("<your_node_url>"));
  HelloWorld helloWorld = HelloWorld.deploy(web3j, Credentials.create("your_private_key"), new DefaultGasProvider(), "Hello Blockchain World").send();
  String greeting = helloWorld.greeting().send();
  web3j.shutdown();
```

There are many ways to create `Credentials` in Web3j, for more information go to the [credentials](./transactions/credentials.md) section.

### Loading a Smart Contract 

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
    
