## Privacy with Hyperledger Besu

The Besu module in Web3j provides support for creating private transactions and privacy groups on Hyperledger Besu. For information as to how privacy is implemented in Besu, please see the [Besu privacy documentation](https://besu.hyperledger.org/en/stable/HowTo/Use-Privacy/EEA-Compliant/).

Privacy in Besu refers to the ability to keep transactions private between the involved participants. Other participants cannot access the transaction content or list of participants. Besu uses Orion, a separate software component, to manage private transactions. Orion is able to maintain public/private keypair, store privacy group details, and discover other Orion nodes on a network. A privacy group is a group of nodes identified by a unique privacy group ID in Orion. Each private transaction is stored in Orion and is associated with the privacy group ID.

The Besu nodes maintain the public world state for the blockchain and a private state for each privacy group. The private states contain data that is not shared in the globally replicated public world state.

Private transactions have additional attributes to public Ethereum transactions:

- `privateFrom` - Orion public key of transaction sender
- `privacyGroupId` - Privacy group to receive transaction
- `restriction` - Private transactions are restricted or unrestricted:
    - In restricted private transactions the payload of the private transaction is received and stored only by the nodes participating in the transaction.
    - In unrestricted private transactions the payload of the private transaction is transmitted to all nodes in the network but is readable only by nodes participating in the transaction.

