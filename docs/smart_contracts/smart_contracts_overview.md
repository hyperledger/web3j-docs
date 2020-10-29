Smart Contracts
===============

Developers have the choice of two languages for writing smart contracts:

[Solidity](https://Solidity.readthedocs.io/)

> The flagship language of Ethereum, and most popular language for smart contracts.

LISP Like Language (LLL)

> A low level language, Serpent provides a superset of LLL. There's not a great deal of information for working with LLL, the following blog [/var/log/syrinx](http://blog.syrinx.net/) and associated [lll-resurrected GitHub](https://github.com/zigguratt/lll-resurrected) repository is a  good place to start.

In order to deploy a smart contract onto the Ethereum blockchain, it must first be compiled into a bytecode format, 
then it can be sent as part of a transaction. Web3j can do all of this for you with its [Solidity smart contract wrappers](construction_and_deployment.md#solidity-smart-contract-wrappers). To understand what is happening behind the scenes, you can refer to the details in [Creation of a smart contract](../transactions/transactions_and_smart_contracts.md#creation-of-a-smart-contract).
Given that Solidity is the language of choice for writing smart contracts, it is the language supported by Web3j, and is used for all subsequent examples.

## Examples

Please refer to [EIP-20 Ethereum token standard smart contract](../getting_started/deploy_interact_smart_contracts.md#eip-20-ethereum-token-standard-smart-contract)
