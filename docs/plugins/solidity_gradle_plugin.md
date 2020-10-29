Solidity Gradle Plugin
======================

Simple Gradle plugin used by the [Web3j plugin](https://github.com/web3j/web3j-gradle-plugin) 
to compile Solidity contracts, but it can be used in any standalone project for this purpose.

## Plugin configuration

To configure the Solidity Gradle Plugin using the plugins DSL or the legacy plugin application, 
check the [plugin page](https://plugins.gradle.org/plugin/org.web3j.solidity). 
The minimum Gradle version to run the plugin is `5.+`.

Then run this command from your project containing Solidity contracts:

```
./gradlew build
```

After the task execution, the base directory for compiled code (by default 
`$buildDir/resources/solidity`) will contain a directory for each source set 
(by default `main` and `test`), and each of those a directory with the compiled code.


## Code generation

The `solidity` DSL allows to configure the generated code, e.g.:

```groovy
solidity {
    outputComponents = [BIN, ABI, ASM_JSON]
    optimizeRuns = 500
}
```

The properties accepted by the DSL are listed in the following table:

|  Name                      | Type                        | Default value                                     | Description                                                     |
|----------------------------|:---------------------------:|:-------------------------------------------------:|-----------------------------------------------------------------|
| `executable`               | `String`                    | `null` (bundled with the plugin)                  | Solidity compiler path.                                         |
| `version`                  | `String`                    | `null` (defined by contract's pragma)             | Solidity compiler version.                                      |
| `overwrite`                | `Boolean`                   | `true`                                            | Overwrite existing files.                                       |
| `resolvePackages`          | `Boolean`                   | `true`                                            | Resolve third-party contract packages.                          |           
| `optimize`                 | `Boolean`                   | `true`                                            | Enable byte code optimizer.                                     |
| `optimizeRuns`             | `Integer`                   | `200`                                             | Set for how many contract runs to optimize.                     |
| `prettyJson`               | `Boolean`                   | `false`                                           | Output JSON in pretty format. Enables the combined JSON output. |
| `ignoreMissing`            | `Boolean`                   | `false`                                           | Ignore missing files.                                           |
| `allowPaths`               | `List<String>`              | `['src/main/solidity', 'src/test/solidity', ...]` | Allow a given path for imports.                                 |
| `pathRemappings`           | `Map<String,String>`        | `[ : ]`                                           | Remaps contract imports to target path.                         |
| `evmVersion`               | `EVMVersion`                | `BYZANTIUM`                                       | Select desired EVM version.                                     |
| `outputComponents`         | `OutputComponent[]`         | `[BIN, ABI]`                                      | List of output components to produce.                           |
| `combinedOutputComponents` | `CombinedOutputComponent[]` | `[BIN, BIN_RUNTIME, SRCMAP, SRCMAP_RUNTIME]`      | List of output components in combined JSON output.              |

**Notes:**

  * Setting the `executable` property will disable the bundled `solc` and use your local or containerized executable:
  
```groovy
solidity {
    executable = "docker run --rm -v $projectDir/src:/src -v $projectDir/build:/build ethereum/solc:0.6.4-alpine"
    version = '0.4.15'
}
```

  * Use `version` to change the bundled Solidity version. 
    Check the [Solidity releases](https://github.com/ethereum/solidity/releases) 
    for all available versions.
  * `allowPaths` contains all project's Solidity source sets by default.

## Source sets

By default, all `.sol` files in `$projectDir/src/main/solidity` will be processed by the plugin.
To specify and add different source sets, use the `sourceSets` DSL. You can also set your preferred
output directory for compiled code.

```groovy
sourceSets {
    main {
        solidity {
            srcDir {
                "my/custom/path/to/solidity"
             }
             output.resourcesDir = file('out/bin/compiledSol') 
        }
    }
}
```

## Gradle Node Plugin

The plugin makes use of the [Node plugin](https://github.com/node-gradle/gradle-node-plugin) to resolve third-party contract dependencies. 
It currently supports:

  * [Open Zeppelin](https://www.npmjs.com/package/@openzeppelin/contracts) 
  * [Uniswap](https://www.npmjs.com/package/@uniswap/lib) 

When importing libraries from `@openzeppeling/contracts` in your Solidity contract the plugin will use the task `resolveSolidity` to generate 
a `package.json` file in order to be used by the [Node plugin](https://github.com/node-gradle/gradle-node-plugin). By default, `package.json` will be generated under the `build/` directory.
If you with do define your own `package.json` you need to add the following snippet in your `build.gradle` file. 

```
node {
    nodeProjectDir = file("my/custom/node/directory")
}
```
The plugin will look for the `package.json` file in the directory set and will also download the node modules under the same directory.

## Plugin tasks

The [Java Plugin](https://docs.gradle.org/current/userguide/java_plugin.html)
adds tasks to your project build using a naming convention on a per source set basis
(i.e. `compileJava`, `compileTestJava`).

Similarly, the Solidity plugin will add a:

   * `resolveSolidity` task for all project Solidity sources.
   * `compileSolidity` task for the project `main` source set.
   * `compile<SourceSet>Solidity` for each remaining source set. (e.g. `compileTestSolidity` for the `test` source set, etc.). 

To obtain a list and description of all added tasks, run the command:

```
./gradlew tasks --all
```
