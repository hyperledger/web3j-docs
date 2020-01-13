Debugging smart contracts and transactions
==========================================

While running locally with the embedded EVM it is possible to debug the internal state when deploying and invoking functions on a contracts.

You do this by giving the embedded Web3j service what's called an operation tracer. We have developed one which allow for console based debugging sessions and code walk throughs called ```ConsoleDebugTracer```.

To enable this simply pass an instance of this in as a second parameter on the ```EmbeddedWeb3jService``` constructor.

```java
OperationTracer operationTracer = new ConsoleDebugTracer();

Web3j web3j = Web3j.build(new EmbeddedWeb3jService(configuration, operationTracer));
```

When your project leverages our [gradle plugin](https://github.com/web3j/web3j-gradle-plugin) the ```ConsoleDebugTracer``` will automatically pick up Solidity contract meta data and use that to display source code context. Usually these meta data json files are stored within ```build/resources/main/solidity```. If you have chosen to place these elsewhere you will need to specify their location when creating the debugger instance:

```java
OperationTracer operationTracer = new ConsoleDebugTracer(new File("alternative/metadata/file/location"));
```
## Options while debugging

The ```ConsoleDebugTracer``` allows you to interact with the debugging session using your terminal. Several options are available as shown below. You can also see these in the terminal by using the ```help``` option.

| Command line option             | Description                                             |
|---------------------------------|---------------------------------------------------------|
| [enter]                         | Continue running until next code section.               |
| [number]                        | Step forward X number of opcodes.                       |
| next                            | Run until the next breakpoint.                          |
| end                             | Run until the end of current transaction.               |
| abort                           | Terminate the function call.                            |
| show\|hide opcodes              | Show or hide opcodes.                                   |
| show\|hide stack                | Show or hide the stack.                                 |
| break [file name] [line number] | Add or remove a breakpoint.                             |
| break list                      | Show all breakpoint.                                    |
| break clear                     | Remove all breakpoint.                                  |

For example, assume I'm debugging a Solidity contract with its source code stored in a file called Greeter.sol.

If I want to have the debugger stop at a specific line in that file I'd add a breakpoint with the break option: ```break Greeter.sol 13```. Hitting enter after adding that breakpoint will make the debugger progress until it reaches line 13 in Greeter.sol.

Likewise, if I only want to see the Solidity source code and don't care to see opcodes and the stack, I'd execute the ```hide opcodes``` and ```hide stack``` commands.