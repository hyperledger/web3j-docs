TLS over HTTP and Websockets
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

Similarly to use TLS over HTTP we will pass modified `OkHttpClient` client to `HttpService`:

```java
// Load the Keystore
final KeyStore keyStore = KeyStore.getInstance(KEYSTORE_TYPE);
keyStore.load(new FileInputStream(KEYSTORE_PATH), KEYSTORE_PASSWORD.toCharArray());

final KeyManagerFactory keyManagerFactory = KeyManagerFactory.getInstance("SunX509");
keyManagerFactory.init(keyStore, KEY_PASSWORD.toCharArray());

final TrustManagerFactory trustManagerFactory = TrustManagerFactory.getInstance("SunX509");
trustManagerFactory.init(keyStore);

final SSLContext sslContext = SSLContext.getInstance("TLS");
sslContext.init(keyManagerFactory.getKeyManagers(), trustManagerFactory.getTrustManagers(), null);

// Create an OkHttpClient with the custom certificate
OkHttpClient client = new OkHttpClient.Builder()
        .sslSocketFactory(sslContext.getSocketFactory(), (X509TrustManager) trustManagerFactory.getTrustManagers()[0])
        .build();

final HttpService httpService = new HttpService("https://<web3j-endpoint>", client, false);
final Web3j web3j = Web3j.build(httpService);
```
Source: [okHttp TLS docs](https://square.github.io/okhttp/features/https/)