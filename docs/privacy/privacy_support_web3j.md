# Privacy



## Privacy Support in Web3j
As mentioned earlier, the Besu module in Web3j can be used to create privacy groups and send private transactions in Besu. The Besu privacy quickstart starts up three nodes with their associated Orion instances, which will henceforth be called Alice, Bob and Charlie for ease of use. The keys associated with each are predetermined, and can be represented in Java as follows:
```java
private static final Credentials ALICE =
        Credentials.create("8f2a55949038a9610f50fb23b5883af3b4ecb3c3bb792cbcefbd1542c692be63");
private static final Credentials BOB =
        Credentials.create("c87509a1c067bbde78beb793e6fa76530b6382a4c0241e5e4a9ec0a0f44dc0d3");
private static final Credentials CHARLIE =
        Credentials.create("ae6ae8e5ccbfb04590405997ee2d52d2b330726137b875053c36d94e974d162f");

private static final Base64String ENCLAVE_KEY_ALICE =
        Base64String.wrap("A1aVtMxLCUHmBVHXoZzzBgPbW/wj5axDpW9X8l91SGo=");
private static final Base64String ENCLAVE_KEY_BOB =
        Base64String.wrap("Ko2bVqD+nNlNYL5EE7y3IdOnviftjiizpjRt+HTuFBs=");
private static final Base64String ENCLAVE_KEY_CHARLIE =
        Base64String.wrap("k2zXEin4Ip/qBGlRkJejnGWdP9cjkK+DAvKNW31L2C8=");
```
The instances of the class `Credentials` above are the private keys associated with the default account of each Besu node in the network. These are configured in the config files for Besu in the privacy quickstart. The `Base64Strings` are the Orion keys associated with each member, and are configured in the config files for Orion in the privacy quickstart. If you choose to create your own network, these values will differ from the defaults as specified in the privacy quickstart.

Web3j provides the class `org.web3j.protocol.besu.Besu` which encapsulates all Besu specific methods with all of the usual Web3j commands. We can use several instances of these to talk to the running quickstart instance:
```java
private static Besu nodeAlice = Besu.build(new HttpService("http://localhost:20000"));
private static Besu nodeBob = Besu.build(new HttpService("http://localhost:20002"));
private static Besu nodeCharlie = Besu.build(new HttpService("http://localhost:20004"));
private static PollingPrivateTransactionReceiptProcessor processor = new PollingPrivateTransactionReceiptProcessor(nodeAlice, 1000, 15);
```

### Creating a privacy group

In order to create a new privacy group, first a random 32 byte base64 string is generated to form the group ID. Then the create group function is called, and we wait for the transaction receipt to be returned. Finally, we can call the `privOnChainFindPrivacyGroup` method, which will find the privacy group which was just created by using the two Orion keys of the group members. 

```java
// Generate a new random Base64 string for the privacy group ID
Base64String privacyGroupId = Base64String.wrap(generateRandomBytes(32));

// Create the privacy group
final String txHash =
        nodeAlice
                .privOnChainCreatePrivacyGroup(
                        privacyGroupId,
                        ALICE,
                        ENCLAVE_KEY_ALICE,
                        Collections.singletonList(ENCLAVE_KEY_BOB))
                .send()
                .getTransactionHash();

// Wait for the transaction to be mined & get the receipt
TransactionReceipt receipt = processor.waitForTransactionReceipt(txHash);

// Find the privacy group based on the two members
Optional<PrivacyGroup> group =
        nodeAlice
                .privOnChainFindPrivacyGroup(
                        Arrays.asList(ENCLAVE_KEY_ALICE, ENCLAVE_KEY_BOB))
                .send()
                .getGroups().stream().filter(x -> x.getPrivacyGroupId().equals(privacyGroupId)).findFirst();
```

### Adding & removing members

After a privacy group has been created, users can be added to the group using the `privOnChainAddToPrivacyGroup` method, and removed from the group using the `privOnChainRemoveFromPrivacyGroup` method.

```java
Base64String privacyGroupId = Base64String.wrap(generateRandomBytes(32));
final String createTxHash =
        nodeAlice
                .privOnChainCreatePrivacyGroup(
                        privacyGroupId,
                        ALICE,
                        ENCLAVE_KEY_ALICE,
                        Collections.singletonList(ENCLAVE_KEY_BOB))
                .send()
                .getTransactionHash();

TransactionReceipt createReceipt = processor.waitForTransactionReceipt(createTxHash);

// Once the group has been created, add Charlie from Alice's node
final String addTxHash =
        nodeAlice
                .privOnChainAddToPrivacyGroup(
                        privacyGroupId,
                        ALICE,
                        ENCLAVE_KEY_ALICE,
                        Collections.singletonList(ENCLAVE_KEY_CHARLIE))
                .send()
                .getTransactionHash();

TransactionReceipt addReceipt = processor.waitForTransactionReceipt(addTxHash);

// Once the add transaction has been mined, find the privacy group which contains Alice, Bob & Charlie
List<PrivacyGroup> groups =
        nodeAlice
                .privOnChainFindPrivacyGroup(
                        Arrays.asList(
                                ENCLAVE_KEY_ALICE, ENCLAVE_KEY_BOB, ENCLAVE_KEY_CHARLIE))
                .send()
                .getGroups();

// Remove Charlie from the privacy group
final String removeTxHash =
        nodeAlice
                .privOnChainRemoveFromPrivacyGroup(
                        privacyGroupId, ALICE, ENCLAVE_KEY_ALICE, ENCLAVE_KEY_CHARLIE)
                .send()
                .getTransactionHash();

TransactionReceipt removeReceipt = processor.waitForTransactionReceipt(removeTxHash);

// Get the privacy group with only Alice and Bob now in it.
List<PrivacyGroup> removedGroups =
        nodeAlice
                .privOnChainFindPrivacyGroup(
                        Arrays.asList(ENCLAVE_KEY_ALICE, ENCLAVE_KEY_BOB))
                .send()
                .getGroups();
```

### Creating & interacting with a smart contract

Once a privacy group has been created, smart contracts can be created & executed within the privacy group. The state of the contract and all of the associated transactions will only be viewable to those within the privacy group. In this example, an instance of a `HumanStandardToken` is deployed within the group, and is visible to each member:

```java
// Create a new privacy group
Base64String aliceBobGroup = Base64String.wrap(generateRandomBytes(32));
final String createTxHash =
        nodeAlice
                .privOnChainCreatePrivacyGroup(
                        aliceBobGroup,
                        ALICE,
                        ENCLAVE_KEY_ALICE,
                        Collections.singletonList(ENCLAVE_KEY_BOB))
                .send()
                .getTransactionHash();

TransactionReceipt createReceipt = processor.waitForTransactionReceipt(createTxHash);

// Find the privacy group that was built by Alice from Bob's node
final Base64String aliceBobGroupFromBobNode =
        nodeBob
                .privOnChainFindPrivacyGroup(
                        Arrays.asList(ENCLAVE_KEY_ALICE, ENCLAVE_KEY_BOB))
                .send().getGroups().stream()
                .filter(g -> g.getPrivacyGroupId().equals(aliceBobGroup))
                .findFirst()
                .orElseThrow(RuntimeException::new)
                .getPrivacyGroupId();

// Create two private transaction manager instances for Alice and Bob in order to interact with smart contracts
final PrivateTransactionManager tmAlice =
        new BesuPrivateTransactionManager(
                nodeAlice,
                ZERO_GAS_PROVIDER,
                ALICE,
                2018,
                ENCLAVE_KEY_ALICE,
                aliceBobGroup);
final PrivateTransactionManager tmBob =
        new BesuPrivateTransactionManager(
                nodeBob,
                ZERO_GAS_PROVIDER,
                BOB,
                2018,
                ENCLAVE_KEY_BOB,
                aliceBobGroupFromBobNode);

// Deploy the token from Alice's node
final HumanStandardToken tokenAlice =
        HumanStandardToken.deploy(
                        nodeAlice,
                        tmAlice,
                        ZERO_GAS_PROVIDER,
                        BigInteger.TEN,
                        "eea_token",
                        BigInteger.TEN,
                        "EEATKN")
                .send();

// Get an instance of the token from Bob's node
final HumanStandardToken tokenBob =
        HumanStandardToken.load(
                tokenAlice.getContractAddress(), nodeBob, tmBob, ZERO_GAS_PROVIDER);
```
