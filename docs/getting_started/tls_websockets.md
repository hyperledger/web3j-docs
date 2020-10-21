TLS over Websockets
-------------------

It is also possible to use TLS with the WebSocketConnection, remember to change your protocol from `ws` to `wss`. For stricter requirements one can define a custom keystore for their SSL certificates by passing in a modified `WebSocketClient` to the `WebSocketService`:

```java
final WebSocketClient webSocketClient = new WebSocketClient(new URI("<WSS-ENDPOINT>"));

final WebSocketService webSocketService =
        new WebSocketService(webSocketClient, false);

// Load the keystore
final KeyStore ks = KeyStore.getInstance(STORETYPE);
final File kf = new File(KEYSTORE_PATH);
ks.load(new FileInputStream(kf), STOREPASSWORD.toCharArray());
final KeyManagerFactory kmf = KeyManagerFactory.getInstance("SunX509");
kmf.init(ks, KEYPASSWORD.toCharArray());
final TrustManagerFactory tmf = TrustManagerFactory.getInstance("SunX509");
tmf.init(ks);

// Create SSL socket
final SSLContext sslContext = SSLContext.getInstance("TLS");
sslContext.init(kmf.getKeyManagers(), tmf.getTrustManagers(), null);
final SSLSocketFactory sslSocketFactory = sslContext.getSocketFactory();

// Use SSL socket in websocket client
webSocketClient.setSocket(sslSocketFactory.createSocket());
webSocketService.connect();

final Web3j web3j = Web3j.build(webSocketService);
```