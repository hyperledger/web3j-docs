# Web3j-OpenAPI

[Web3j-OpenAPI](https://github.com/web3j/web3j-openapi) is an [OpenAPI](https://swagger.io/specification/) generator from [Solidity](https://solidity.readthedocs.io/) smart contracts. It provides a way to interact with the [Ethereum](https://ethereum.org/) blockchain via simple and intuitive HTTP requests. These interactions can be done using plain HTTP requests or via the [Swagger-UI](https://swagger.io/tools/swagger-ui/), which is generated with every project.

This workflow can be summed in the following steps:
- Writing a solidity smart contract
- Generating the corresponding OpenAPI project using Web3j-OpenAPI
- Running the generated project
- Sending HTTP requests using Curls or Swagger-UI

## Example
The following Hello World contract :

```
pragma solidity ^0.6.0;

contract HelloWorld {
 function hello() public pure returns (string memory) {
   return 'Hello Web3j-OpenAPI';
 }
 
 function sayIt(string memory greeting) public pure returns (string memory) {
   return greeting;
 }
}
```

generates the following `OpenAPI` specs : 

```
{
  "openapi": "3.0.1",
  "info": {
    "title": "Web3j OpenApi",
    "contact": {
      "name": "Web3 Labs",
      "url": "http://web3labs.com",
      "email": "hi@web3labs.com"
    }
  },
  "tags": [
    {
      "name": "default",
      "description": "List existing contracts names"
    },
    {
      "name": "HelloWorld",
      "description": "List HelloWorld method&#39;s calls"
    }
  ],
  "paths": {
    "/HelloWorldProject/contracts/helloworld": {
      "post": {
        "tags": [
          "HelloWorld"
        ],
        "summary": "Deploys the HelloWorld contract",
        "operationId": "deploy",
        "responses": {
          "default": {
            "description": "default response",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/TransactionReceipt"
                }
              }
            }
          }
        }
      }
    },
    ...
```

With a corresponding `Swagger-UI` : 
![image](img/Web3j-OpenAPI/SwaggerUI_1.png)

and the available methods:

![image](img/Web3j-OpenAPI/SwaggerUI_2.png)

# Why an OpenAPI generator?

As stated above, the Web3j-OpenAPI generator creates an OpenAPI project from a smart contract. Thus, providing a way to interact with the Ethereum blockchain using HTTP requests.
Such a generator is beneficial in the following ways:
### By making it easier to interact with the Ethereum blockchain:
Before this, interacting with the Ethereum blockchain required knowledge of a programming language other than Solidity, and then hardcoding the desired logic.This made it hard for people wishing to get involved in the smart contracts world and added extra technical requirements aside from knowing the language of smart contracts.

### By Interacting with smart contracts without code:
Being able to generate an OpenAPI project from a smart contract and interacting with it using HTTP requests, eliminates the need to code any interactions to be able to send them to the blockchain. Therefore, by using the Web3j-OpenAPI, it’s possible to interact with the Ethereum blockchain using no code, besides Solidity.


## Use cases
An OpenAPI project can be generated using the [Epirus-CLI](https://github.com/epirus-io/epirus-cli) as follows:

```ssh
$ epirus generate openapi -p com.helloworld -o . --abi helloworld.abi --bin helloworld.bin --name helloworld
```

Then, the generated project can be used in the following ways:

**Creating a Gradle project that can be run using the application plugin:**
```ssh
$ cd helloworld
$ ./gradlew run // Starts the server exposing Helloworld.sol
...
```

**Starting the server using the ShadowJar:**
```ssh
$ ./gradlew shadowJar // Create an executable JAR that starts the server
$ java -jar helloworld-server-all.jar
...
```

**Using the CLI:**

```ssh
$ ./gradlew installDist // Create an executable to start the server
$ ./helloworld-server
```

# Getting started with Web3j-OpenAPI

## How to generate a project:
To generate an OpenAPI project using the [Web3j-OpenAPI](https://github.com/web3j/web3j-openapi) generator, you need to have the [Epirus-CLI](https://github.com/epirus-io/epirus-cli) installed on your machine. 
It’s easy to do:
	
```
curl -L get.epirus.io | sh
```

And then, you can run the
```
Epirus generate openapi --help
```
To check the generator available options. 

![image](img/Web3j-OpenAPI/Generator_help.png)

In this case, we will be using the above Hello World contract. 
To compile it, you can use solc via SVM(link). This lets you handle multiple solidity compiler versions easily:
```
Svm install 0.6.0
Svm use 0.6.0
Solc --abi --bin -o . HelloWorld.sol
```

Now, you should  see the ABI and Bin files in the directory. You  can use them to generate the OpenAPI:
```	
Epirus generate openapi --abi HelloWorld.abi --bin HelloWorld.bin --package-name com.tutorial --output . --name HelloWorldProject
```
You should be seeing logs similar to the following:


## How to build

You have two possibilities:

#### Generate the ShadowJAR
`./gradlew shadowJar` Which you can find in the `server/build/libs` folder.

#### Generate the distributions
`./gradlew installDist` Which you can find in the `server/build/install/HelloWorldProject-server/bin` folder.

## Configurations
In all cases, we need to specify the runtime configuration. 

For example, the `private key` or `wallet file` for the signing, the `node endpoint` to connect to, etc.

To see the available options, try to run the `distributions` or the `JAR` with the `--help` flag. You'll get the following display:

![image](img/Web3j-OpenAPI/Server_help.png)

To specify these parameters, we have three ways:

#### Environment variables
The rule is to:
- Replace the `-` with a `_` : `private-key => private_key`
- Upper case the options' names : `private-key => PRIVATE_KEY`

And export them:
	`Export PRIVATE_KEY={your private key} `

#### Configuration file
We can pass in a configuration file in two ways:

##### Via an environment variable
```
Export CONFIG_FILE={the path to the config file}
```
##### Via the CLI
```
--config--file {path to file}
```
Also, there is the possibility to put the config file in the default directory: `~/.epirus/web3j.openapi.properties`

A configuration file is similar to the following:
```
web3j.openapi.private.key={your private key}
web3j.openapi.port=9090
```
#### Directly from the CLI
The usual way:  `--private-key {your private key}`

Then, we are ready to run the project.

## Run the project
We can run the project directly : `./gradlew run`
However, specifying the runtime parameters in this case from the CLI is tricky. For this case, either use environment variables or Default Configuration file as stated above.

#### Run the FatJAR
```ssh
$ java -jar server/build/libs/HelloWorldProject-server-all.jar [arguments]
```

#### Run the distributions binary
```ssh
$ ./server/build/install/HelloWorldProject-server/bin/HelloWorldProject-server [arguments]
```

You should be able to run the server and see the following:

![image](img/Web3j-OpenAPI/Server_logs.png)

## Interact with the generated project:
Interactions can be done using HTTP requests either through `Curls`:
```ssh
$ curl -X POST "http://{host}:{port}/{application name}/contracts/helloworld/{contractAddress}/SayIt" -H  "accept: application/json" -H  "Content-Type: application/json" -d "{\"greeting\":\"Hello OpenAPI\"}"
```
Or, the `SwaggerUI`, on the link `{host}:{port}/swagger-ui`:

![image](img/Web3j-OpenAPI/SwaggerUI_3.png)

To interact via Java/Kotlin:

```groovy
dependencies {
    implementation "web3j-openapi:web3j-openapi-client:0.1.0"
}
```

And within a client application:

```
val service = ClientService("http://localhost:8080")
val helloWorld = ClientFactory.create(HelloWorldApi::class.java, service)

val receipt = helloWorld.contracts.greeter.deploy(
    GreeterDeployParameters("Test greeter")
)

val greeter = helloWorld.contracts.greeter.load(receipt.contractAddress)
```