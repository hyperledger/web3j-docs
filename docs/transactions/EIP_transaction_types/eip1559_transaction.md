## EIP-1559 Transaction

Ethereum historically priced transaction fees using a simple auction mechanism, where users send transactions with bids 
("gasprices") and miners choose transactions with the highest bids, and transactions that get included pay the bid that
they specify. This leads to several large sources of inefficiency: 

- Mismatch between volatility of transaction fee levels and social cost of transactions
- Needless delays for users
- Inefficiencies of first price auctions
- Instability of blockchains with no block reward

With EIP-1559, there will be a discrete “base fee” for transactions to be included in the next block. For users or 
applications that want to prioritize their transaction, they can add a “tip,” which is called a “priority fee” to pay a 
miner for faster inclusion.

### Spec
Introduce a new EIP-2718 transaction type, with value `2(0x02)` and `TransactionPayload` for this transaction is `rlp([chain_id, 
nonce, max_priority_fee_per_gas, max_fee_per_gas, gas_limit, destination, amount, data, access_list, signature_y_parity, 
signature_r, signature_s])`.

The `GASPRICE (0x3a)` opcode return the `effective_gas_price`.

### Example

```java

public void signedEip1559() throws SignatureException {
        Credentials credentials = Credentials.create("<privateKey>");
        Web3j web3j = Web3j.build(new HttpService("<nodeUrl>"));
        
        final RawTransaction rawTransaction = createEip1559RawTransaction();

        final byte[] signedMessage =
                TransactionEncoder.signMessage(rawTransaction, credentials);
        final String signedHexMessage = Numeric.toHexString(signedMessage);

        EthSendTransaction ethSendTransaction = web3j.ethSendRawTransaction(signedHexMessage).send();

        System.out.println("Transaction hash: " + ethSendTransaction.getTransactionHash());
        System.out.println("Tx Receipt = " + web3j.ethGetTransactionReceipt(ethSendTransaction.getTransactionHash()).send().getTransactionReceipt());
        
    }

private static RawTransaction createEip1559RawTransaction() {
        return RawTransaction.createEtherTransaction(
                3L,
                BigInteger.valueOf(0),
                BigInteger.valueOf(30000),
                "0x627306090abab3a6e1400e9345bc60c78a8bef57",
                BigInteger.valueOf(123),
                BigInteger.valueOf(5678),
                BigInteger.valueOf(1100000));
        }
```