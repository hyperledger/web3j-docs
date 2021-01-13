Manual Configuration
===============

The simplest way to get started with Web3j is via the powerful [Web3j CLI](http://docs.web3j.io/latest/command_line_tools/). 

However, if you wish to configure your project manually, you can follow the steps outlined here.

Add the latest Web3j version to your project build configuration.

## Maven

Java:

``` xml
<dependency>
  <groupId>org.web3j</groupId>
  <artifactId>core</artifactId>
  <version>4.8.1</version>
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

## Gradle

Java:

``` groovy
compile ('org.web3j:core:4.8.1')
```

Android:

``` groovy
compile ('org.web3j:core:4.6.0-android')
```

## Plugins 

There are also gradle and maven plugins to help you generate Web3j Java wrappers for your Solidity smart contracts, thus allowing you to integrate such activities into your project lifecycle.
Take a look at the project homepage for the [web3j-gradle-plugin](https://github.com/web3j/web3j-gradle-plugin)
 and [web3j-maven-plugin](https://github.com/web3j/web3j-maven-plugin) for details on how to use these plugins.

