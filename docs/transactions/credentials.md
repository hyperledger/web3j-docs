### Create Credentials

There are two ways to create credentials in Web3j.

### Use Credentials object

- `Credentials.create(ECKeyPair ecKeyPair)`
- `Credentials.create(String privateKey, String publicKey)`

### Use WalletUtils functionality

The `WalletUtils` class cna be used to load your credentials from various formats:

- `WalletUtils.loadCredentials(String password,String source)`
- `WalletUtils.loadBip39Credentials(String password,String mnemonic)`
- `WalletUtils.loadBip39Credentials(String password,String mnemonic)`
- `WalletUtils.loadJsonCredentials(String password,String content)`
