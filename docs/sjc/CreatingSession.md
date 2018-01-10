---
nav-sort: 111
nav-level: 1
---

# Creating a Symphony Session

All calls into Symphony host systems require authorization and session keys.

In order to obtain keys, the application requires the following:

* **BOT Client Certificate** (p12), where the common name (CN=) is equal to the bot user name.  The certificate password is also required.
* **Server Truststore file**, contains the certificate authority certs for all servers the application will connect to.  The truststore password is also required.
* **Keymanager URL**, this could be both Symphony provided on the host POD or local HSM backed.
* **Session Authorization URL**, where session tokens are generated

The SDK makes it easy to generate an authorization model through use of the **AuthorizationClient**.
>

    //Model holding generated session keys.
    SymAuth symAuth;

    //Init the Symphony authorization client, which requires both the key and session URL's.  In most cases,
    //the same fqdn but different URLs.
    AuthorizationClient authClient = new AuthorizationClient(
    System.getProperty("sessionauth.url"),
    System.getProperty("keyauth.url"));


    //Set the local keystores that hold the server CA and client certificates
    authClient.setKeystores(
    System.getProperty("truststore.file"),
    System.getProperty("truststore.password"),
    System.getProperty("certs.dir") + System.getProperty("bot.user") + ".p12",
    System.getProperty("keystore.password"));

    //Create a SymAuth which holds both key and session tokens.  This will call the external service.
    symAuth = authClient.authenticate();


### Using a custom HTTP Client

In the event that you require a different http connectivity requirements, such as a proxy or ignoring server cert validation, the AuthorizationClient class supports using a custom HttpClient through the following constructor.

    AuthorizationClient(SessionURL, KeyauthURL, HttpClient)


To help in creating a custom http client, the SDK provides a helper utility which also removes the need to set certificate keystores in the AuthorizationClient.  The **org.symphonyoss.client.impl.CustomHttpClient** provides static methods to instantiate custom http client with custom properties.


            try {
                //Proxy config example
                ClientConfig clientConfig = new ClientConfig();
                clientConfig.connectorProvider(new ApacheConnectorProvider());
                clientConfig.property(ClientProperties.PROXY_URI, "https://wwwproxy:8080");  //Or change to http..etc.
                Client httpClient = CustomHttpClient.getClient(clientKeyStore,clientKeyStorePass,trustStore,trustStorePass,clientConfig);

                AuthorizationClient authClient = new AuthorizationClient(
                    System.getProperty("sessionauth.url"),
                    System.getProperty("keyauth.url"),
                    httpClient);


                //Create a SymAuth which holds both key and session tokens.  This will call the external service.
                SymAuth symAuth = authClient.authenticate();

               } catch (Exception e) {
                logger.error("Failed to obtain auth...",e);

               }

Next Topic: [Streams](https://github.com/symphonyoss/symphony-java-client/wiki/Streams)