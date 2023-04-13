Json Web Key (JWK) support
==================

In our days Json Web Key (JWK) files has started to be used more and more. Especially in web3 space where asymetric encryption is used for cryptographical proof, one example is in Decentralised Identity model on verifiable credentials.

These types of files are derived from public-private key pairs which where previously generated under a certain algorithm like ECSDA or RSA. The coordinates are extracted according to the curved of the keys, from public key the coordinates x and y, from private key the coordinate d. Coordinates once obtained can be easily added to the public and private JWK file. Notice that the difference between public and private JWK is that the private file contains coordinate d.

Asymmetric keys in Web3j
-----------------------------

So far Web3J was able to support generation of public-private key pair specific for EVM addresses. These key were generated using java.security and created as specific `org.bouncycastle.jcajce.provider.asymmetric.ec.BCECPublicKey` and private key. These are generated using ECSDA algoritm under `secp256k1` curve, EVM specific.

Starting with version `4.9.8` Secp256k1JWK was introduced. This class has the builder capabilities to directly generate the public or private jwk files that can be further used.

Generate public JWK:
-----------------------------

```java
KeyPair keyPair = Keys.createSecp256k1KeyPair();
BCECPublicKey publicKey = (BCECPublicKey) keyPair.getPublic();
Secp256k1JWK jwk = new Secp256k1JWK.Builder(publicKey).build();

ObjectMapper mapper = new ObjectMapper();
System.out.println(mapper.writeValueAsString(jwk));
```

Generate private JWK:
-----------------------------

```java
KeyPair keyPair = Keys.createSecp256k1KeyPair();
BCECPublicKey publicKey = (BCECPublicKey) keyPair.getPublic();
BCECPrivateKey privateKey = (BCECPrivateKey) keyPair.getPrivate();
Secp256k1JWK jwk = new Secp256k1JWK.Builder(publicKey).withPrivateKey(privateKey).build();

ObjectMapper mapper = new ObjectMapper();
System.out.println(mapper.writeValueAsString(jwk));
```
