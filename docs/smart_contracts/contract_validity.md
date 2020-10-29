Contract validity
-----------------

Using this method, you may want to ascertain that the contract address that you have loaded is the smart contract that you expect. For this you can use the *isValid* smart contract method, which will only return true
if the deployed bytecode at the contract address matches the bytecode in the smart contract wrapper.:

```java
contract.isValid();  // returns false if the contract bytecode does not match what's deployed
                     // at the provided address
```

Note: Contract wrapper has to be generated with *--bin* for this to work.
