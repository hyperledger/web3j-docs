Getting Started
===============

The simplest way to get started with Web3j is via the powerful [Epirus CLI](https://docs.epirus.io/sdk/cli/). 

However, if you wish to configure your project manually, you can follow the steps outlined here.

Add the latest web3j version to your project build configuration.

Maven
-----

Java:

``` xml
<dependency>
  <groupId>org.web3j</groupId>
  <artifactId>core</artifactId>
  <version>4.7.0</version>
</dependency>
```

Android:

``` xml
<dependency>
  <groupId>org.web3j</groupId>
  <artifactId>core</artifactId>
  <version>4.6.0-android</version>
</dependency>
```

Gradle
------

Java:

``` groovy
compile ('org.web3j:core:4.7.0')
```

Android:

``` groovy
compile ('org.web3j:core:4.6.0-android')
```

Plugins 
-------
There are also gradle and maven plugins to help you generate web3j Java wrappers for your Solidity smart contracts, thus allowing you to integrate such activities into your project lifecycle.

Take a look at the project homepage for the [web3j-gradle-plugin](https://github.com/web3j/web3j-gradle-plugin) and [web3j-maven-plugin](https://github.com/web3j/web3j-maven-plugin) for details on how to use these plugins.

Further details
---------------

In the Java  build:

-   Web3j provides type safe access to all responses. Optional or null responses are wrapped in Java's [Optional](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html) type.
-   Asynchronous requests are wrapped in a Java [CompletableFutures](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html). web3j provides a wrapper around all async requests to ensure that any exceptions during execution will be captured rather then silently discarded. This is due to the lack of support in *CompletableFutures* for checked exceptions, which are often rethrown as unchecked exception causing problems with detection. See the [Async.run()](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/utils/Async.java) and its associated [test](https://github.com/web3j/web3j/blob/master/core/src/test/java/org/web3j/utils/AsyncTest.java) for details.

In both the Java and Android builds:

-   Quantity payload types are returned as [BigIntegers](https://docs.oracle.com/javase/8/docs/api/java/math/BigInteger.html). For simple results, you can obtain the quantity as a String via [Response](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/protocol/core/Response.java).getResult().
-   It's also possible to include the raw JSON payload in responses via the *includeRawResponse* parameter, present in the [HttpService](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/protocol/http/HttpService.java) and [IpcService](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/protocol/ipc/IpcService.java) classes.
