## Gas

When a transaction takes place in Ethereum, a transaction cost must be paid to the client that executes the transaction on your behalf, committing the output of this transaction to the Ethereum blockchain.

This cost is measure in gas, where gas is the number of instructions used to execute a transaction in the Ethereum Virtual Machine. Please refer to the [Homestead
documentation](http://ethdocs.org/en/latest/contracts-and-transactions/account-types-gas-and-transactions.html?highlight=gas#what-is-gas) for further information.

What this means for you when working with Ethereum clients is that there are two parameters which are used to dictate how much Ether you wish to spend in order for a transaction to complete:

_Gas price_

> This is the amount you are prepared in Ether per unit of gas. web3j
> uses a default price of 22,000,000,000 Wei (22 x 10^-8^ Ether). This
> is defined in
> [ManagedTransaction](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/tx/ManagedTransaction.java).

_Gas limit_

> This is the total amount of gas you are happy to spend on the
> transaction execution. There is an upper limit of how large a single
> transaction can be in an Ethereum block which restricts this value
> typically to less then 6,700,000. The current gas limit is visible at
> <https://ethstats.net/>.

These parameters taken together dictate the maximum amount of Ether you are willing to spend on transaction costs. i.e. you can spend no more then gas price \* gas limit. The gas price can also affect how quickly a transaction takes place depending on what other transactions are available with a more profitable gas price for miners.

You may need to adjust these parameters to ensure that transactions take place in a timely manner.

### Picking Gas Providers

The public Interface `ContractGasProvider` has two implementations that can be used in order to pass in a gas provider to the deploy method of the contract.

The `DefaultGasProvider` is pre-defined implementation of the `ContractGasProvider` that has set values for gas price and gas limit.

- `GAS_LIMIT = BigInteger.valueOf(9_000_000);`
- `GAS_PRICE = BigInteger.valueOf(4_100_000_000L));`

If a more flexible approach is required then the `StaticGasProvider` can take custom gas price and gas limit values as BigIntegers.
