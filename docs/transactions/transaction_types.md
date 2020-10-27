## Transaction types

The different types of transaction in web3j work with both Transaction and RawTransaction objects. The key difference is that Transaction objects must always have a from address, so that the Ethereum client
which processes the [eth_sendTransaction](https://eth.wiki/json-rpc/API#eth_sendtransaction) request know which wallet to use in order to sign and send the
transaction on the message senders behalf. As mentioned [above](transaction_mechanisms.md#offline-transaction-signing), this is not necessary for raw transactions which are signed offline.

The subsequent sections outline the key transaction attributes required for the different transaction types. The following attributes remain constant for all:

- Gas price
- Gas limit
- Nonce
- From

Transaction and RawTransaction objects are used interchangeably in all of the subsequent examples.
