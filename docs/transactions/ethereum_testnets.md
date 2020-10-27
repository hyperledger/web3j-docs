## Ethereum testnets

There are a number of dedicated test networks in Ethereum, which are
supported by various clients.

- Goerli (All clients)
- Rinkeby (Geth only)
- Kovan (OpenEthereum only)
- Ropsten (Geth and OpenEthereum)

For development, its recommended you use the Rinkeby or Kovan test networks. This is because they use a Proof of Authority (PoA) consensus mechanism, ensuring transactions and blocks are created in a consistent and timely manner. The Ropsten testnet, although closest to the Mainnet as it uses Proof of Work (PoW) consensus, has been subject to attacks in the past and tends to be more problematic for developers.

You can request Ether for the Rinkeby testnet via the Rinkeby Crypto Faucet, available at <https://www.rinkeby.io/#faucet>.

Details of how to request Ether for the Kovan testnet are available [here](https://github.com/kovan-testnet/faucet).

(This section needs to be changed)
If you need some Ether on the Ropsten testnet to get started, please post a message with your wallet address to the [Web3j Community Forum](https://community.web3labs.com/) and you will be sent some.

## Mining on testnet/private blockchains

In the Ethereum test environment (testnet), the mining difficulty is set lower then the main environment (mainnet). This means that you can mine new Ether with a regular CPU, such as your laptop. What you'll need to do is run an Ethereum client such as Geth, Besu or OpenEthereum to start building up reserves. Further instructions are available on the respective sites.

Geth

<https://geth.ethereum.org/docs/interface/mining>

Besu

<https://besu.hyperledger.org/en/stable/Concepts/Mining/>

OpenEthereum

<https://openethereum.github.io/wiki/Mining>

Once you have mined some Ether, you can start transacting with the blockchain.

However, as mentioned [above](#ethereum-testnets) it's simpler to use the Kovan or Rinkeby test networks.
