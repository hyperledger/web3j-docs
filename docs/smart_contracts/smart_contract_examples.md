Smart contract examples
-----------------------

Web3j provides a number of smart contract examples in the project directory [codegen/src/test/resources/solidity](https://github.com/web3j/web3j/tree/master/codegen/src/test/resources/solidity)

It also provides integration tests for demonstrating the deploying and working with those smart contracts in the [integration-tests/src/test/java/org/web3j/protocol/scenarios](https://github.com/web3j/web3j/tree/master/integration-tests/src/test/java/org/web3j/protocol/scenarios) module.

![image](../img/smart_contract.png)

EIP-20 Ethereum token standard smart contract 
---------------------------------------------

There an Ethereum standard, [EIP-20](https://eips.ethereum.org/EIPS/eip-20) which started off as an [Ethereum Improvement Proposal
(EIP)](https://github.com/ethereum/EIPs), that defines the standard functions that a smart contract providing tokens should implement.

The EIP-20 standard provides function definitions, but does not provide an implementation example. However, there is an implementation provided in
[codegen/src/test/resources/solidity/contracts](https://github.com/web3j/web3j/tree/master/codegen/src/test/resources/solidity/contracts), which has been taken from ConsenSys' [Tokens project](https://github.com/ConsenSys/Tokens).

Open Zepplin also provide an example implementation on [GitHub](https://github.com/OpenZeppelin/zeppelin-solidity/tree/master/contracts/token).

There are two integration tests that have been written to fully demonstrate the functionality of this token smart contract.

[HumanStandardTokenGeneratedIT](https://github.com/web3j/web3j/tree/master/integration-tests/src/test/java/org/web3j/protocol/scenarios/HumanStandardTokenGeneratedIT.java) uses the generated HumanStandardTokenGenerated [Solidity smart contract wrappers](#solidity-smart-contract-wrappers) to demonstrate this.

Alternatively, if you do not wish to use a smart contract wrapper and would like to work directly with the underlying JSON-RPC calls, please refer to [HumanStandardTokenIT](https://github.com/web3j/web3j/tree/master/integration-tests/src/test/java/org/web3j/protocol/scenarios/HumanStandardTokenIT.java).

