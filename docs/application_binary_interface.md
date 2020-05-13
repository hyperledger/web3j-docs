Application Binary Interface
============================

The Application Binary Interface (ABI) is a data encoding scheme used in Ethereum for working with smart contracts. The types defined in the ABI are the same as those you encounter when writing [Smart Contracts](smart_contracts.md) with Solidity - i.e. *uint8, ..., uint256, int8, ..., int256, bool, string,* etc.

The [ABI module](https://github.com/web3j/web3j/tree/master/abi) in web3j provides full support for the ABI specification, and includes:

-   Java implementations of all ABI types, including conversion from and to native Java types
-   Function and event support
-   ABIv2 support
-   Plenty of unit tests

Type mappings
-------------

The native Java to ABI type mappings used within web3j are as follows:

-   boolean -> bool
-   BigInteger -> uint/int
-   byte[\] -> bytes
-   String -> string and address types
-   List<\> -> dynamic/static array
-   T -> struct/tuple types

BigInteger types have to be used for numeric types, as numeric types in Ethereum are 256 bit integer values.

[Fixed point types](http://solidity.readthedocs.io/en/develop/abi-spec.html#types) have been defined for Ethereum, but are [not currently implemented in Solidity](https://github.com/ethereum/solidity/issues/409), hence web3j does not currently support them (they were provided in versions prior to 3.x). Once available in Solidity, they will be reintroduced back into the web3j ABI module.

Solidity structs will have a corresponding class generated for them. When using ABI generated from solidity compiler version 0.5.x or less, the names of the corresponding classes will be arbitrary i.e. `struct Foo` in your contract will be called `StructN` in the smart contract wrapper, where `N` in a number greater than 0. When using ABI generated from solidity compiler 0.6.x or more, the names of the corresponding classes will be the same as the name of the struct in the Solidity contract i.e. `struct Foo` in your smart contract will be called `Foo` in the smart contract wrapper.

For more information on using ABI types in Java, refer to [Solidity smart contract wrappers](smart_contracts.md#solidity-smart-contract-wrappers).

Further details
---------------

Please refer to the various [ABI unit tests](https://github.com/web3j/web3j/tree/master/abi/src/test/java/org/web3j/abi) for encoding/decoding examples.

A full ABI specification is maintained with the [Solidity documentation](http://solidity.readthedocs.io/en/develop/abi-spec.html).

Dependencies
------------

This is a very lightweight module, with the only third-party dependency being [Bouncy Castle](https://www.bouncycastle.org/) for cryptographic hashing ([Spongy Castle](https://rtyley.github.io/spongycastle/) on Android). The hope is that other projects wishing to work with
Ethereum's ABI on the JVM or Android will choose to make use of this module rather then write their own implementations.