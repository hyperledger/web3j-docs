Quickstart
==========

Web3j CLI
---------

Install the Web3j binary.

To get the latest version on Mac OS or Linux, type the following in your terminal:

```bash
curl -L https://get.web3j.io | sh
```

Then to create a new project, simply run:

```bash
web3j new
```

Or, to import an existing Solidity project into Web3j, run:

```bash
web3j import
```
​
The first step after creating a new project is to fund your wallet.
The wallet's address will be printed on the console after the project is created.
​
To fund your wallet you can go to `https://rinkeby.faucet.epirus.io/` and paste in your wallet address.
​
After your wallet is funded there are 3 ways to execute your code.
​
#### Using Gradle run to execute your code:
​
One way you can execute your code is by running `./gradlew run`.
​
When executing your application with ./gradlew run you need to set up your Ethereum node URL as an environmental variable as such:
​
- `export NODE_URL= https://example.com` 
​
#### Using java -jar to execute your code
​
When executing your application as a jar you can use:
​
```
java -DNODE_URL=<URL_TO_NODE> -jar ./build/libs/Web3App-0.1.0-all.jar
```
​
Make sure to replace <URL_TO_NODE> with the URL of your Ethereum node.
​
#### Executing your code from the IDE
​
If you want to execute your code from the IDE all you need to do is add your Ethereum node URL to:
​
```
Web3j web3j = createWeb3jService(<URL_TO_NODE>);
```
​
#### Making changes to you Solidity files
​
If you one to make changes to you solidity files and re-generate the java wrappers you can run:
​
```
./gradlew generateContractWrappers
```
​
This will re-generate your java contract wrappers.
Then to build your project run:

```bash
./gradlew build
```

For more information on using the Web3j CLI, head to the [CLI section](command_line_tools.md).


Sample project
--------------

A [web3j sample project](https://github.com/web3j/sample-project-gradle) is available that demonstrates a number of core features of Ethereum with web3j, including:

-   Connecting to a node on the Ethereum network
-   Loading an Ethereum wallet file
-   Sending Ether from one address to another
-   Deploying a smart contract to the network
-   Reading a value from the deployed smart contract
-   Updating a value in the deployed smart contract
-   Viewing an event logged by the smart contract
