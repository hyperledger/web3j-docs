Start a client
--------------

Start up an Ethereum client if you don't already have one running.

[Geth](https://geth.ethereum.org/docs/getting-started):

``` bash
$ geth --rpcapi personal,db,eth,net,web3 --rpc --testnet
```

[Hyperledger Besu](https://besu.hyperledger.org/en/stable/HowTo/Get-Started/Starting-node/#run-a-node-for-testing):


``` bash
$ besu ----network=dev
```

The `dev` network uses has [some handy default parameters](https://besu.hyperledger.org/en/stable/Reference/Accounts-for-Testing/#development-mode).


[OpenEthereum](https://openethereum.github.io/wiki/Basic-Usage):

``` bash    
$ openethereum --chain testnet  
```

Instructions on obtaining Ether to transact on the network can be found in the
[testnet section of the docs](` https://docs.web3j.io/transactions/#ethereum-testnets`).

