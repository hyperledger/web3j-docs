# Sokt

Sokt is a Kotlin wrapper for the Solidity compiler (solc). Given a solidity file, it can identify the ideal compiler version to use from the pragma statement at the top of the file. It can then download, install and invoke the compiler. Rather than using Dockerized versions of Solc, Sokt uses native builds and is compatible with Mac, Windows and Linux (x86/64 only)<sup>*</sup>. This means that the only dependency is a Java installation. Sokt also plays nicely with GraalVM, eliminating the Java dependency if necessary.

Sokt is currently under active development. You can use it in one of your own projects by adding the following dependency to your `build.gradle`:

```groovy
dependencies {
    compile group: 'org.web3j', name: 'web3j-sokt', version: '{{ sokt.version }}'
}
```
For other build systems, see [here](https://mvnrepository.com/artifact/org.web3j/web3j-sokt/0.2.1).

Example usage:
```java
  String fileName = filePath.substringAfterLast("/");
  System.out.println("sokt Processing " + fileName);
  SolidityFile solidityFile = new SolidityFile(filePath);
  System.out.println("Resolving compiler version for " + fileName);
  SolcInstance compilerInstance = solidityFile.getCompilerInstance();

  System.out.println("Resolved" + compilerInstance.getSolcRelease().getVersion() + " for " + fileName);

  SolcOutput result = compilerInstance.execute(
    SolcArguments.OUTPUT_DIR.param(() -> "/tmp"),
    SolcArguments.AST,
    SolcArguments.BIN,
    SolcArguments.OVERWRITE
    );

  System.out.println("Solc exited with code: " + result.getExitCode());
  System.out.println("Solc standard output: " + result.getStdOut());
  System.out.println("Solc standard error: " + result.getStdErr());

```

<sup>*</sup>Note: Solc is not officially released for Linux arm machines, You can build it from source and copy the bin file to `{$USER}/.web3j/solc/<solc-version>/`

How to build from source, see [here](https://docs.soliditylang.org/en/latest/installing-solidity.html#building-from-source).