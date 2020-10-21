Quickstart
==========

Install the Epirus binary.

To get the latest version on Mac OS or Linux, type the following in your terminal:

```bash
curl -L get.epirus.io | sh && source ~/.epirus/source.sh
```

Then to create a new project, simply run:

```bash
epirus new
```

Or, to import an existing Solidity project into a Web3j project, run:

```bash
epirus import
```

Then to build your project run:

```bash
./gradlew build
```

For more information on using the Epirus CLI, head to the [Epirus Quickstart](https://docs.epirus.io/quickstart/#installation).


Sample project
--------------

For the most current experience, we recommend you follow the [Epirus Quickstart](https://docs.epirus.io/quickstart/#installation) mentioned above. However, if you'd like to see a bare-bones project, you can refer to the [Web3j sample project](https://github.com/web3j/sample-project-gradle) is available that demonstrates a number of core features of Ethereum with web3j, including:

-   Connecting to a node on the Ethereum network
-   Loading an Ethereum wallet file
-   Sending Ether from one address to another
-   Deploying a smart contract to the network
-   Reading a value from the deployed smart contract
-   Updating a value in the deployed smart contract
-   Viewing an event logged by the smart contract
