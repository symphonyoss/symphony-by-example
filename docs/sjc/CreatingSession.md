---
nav-sort: 111
nav-level: 1
---

# Creating a Symphony Session

All calls into Symphony host systems require authorization and session keys.

In order to obtain keys, the application requires the following:

* **BOT Client Certificate** (p12), where the common name (CN=) is equal to the bot user name.  The certificate password is also required.
* **Server Truststore file**, contains the certificate authority certs for all servers the application will connect to.  The truststore password is also required.
* **Key Manager URL**, this could be both Symphony provided on the host POD or local HSM backed.
* **Session Authorization URL**, where session tokens are generated

The SDK makes it easy to generate authorization tokens through use of the **AuthenticationClient**.  With this said, the fastest way is to both initialize and generate a client instance is using the **SymphonyClientConfig**.  This config will search for and load all required properties from properties file, jvm options or environment variables.
The key properties are:


    -Dsessionauth.url=https://(company name)-api.symphony.com/sessionauth
    -Dkeyauth.url=https://(company name)-api.symphony.com/keyauth

    -Duser.email=(Bot user email address)
    -Duser.cert.password=(Bot user certificat password)
    -Duser.cert.file=(Bot user p12 certificate file)

    -Dtruststore.password=
    -Dtruststore.file=(CA Root cert file)

    -Dpod.url=https://(company name).symphony.com/pod
    -Dagent.url=https://(local agent server fqdn):8446/agent

If loading from a properties file use:

    -Dsymphony.config.file=(config file)

If using environment variables, set property names to uppercase and replace "." with "_"*:

    user.cert.file => USER_CERT_FILE

To create a session and initialize a client instance:


     try {
                //Create a bootstrap configuration, which will load properities for you..
                SymphonyClientConfig symphonyClientConfig = new SymphonyClientConfig(true);


                //Create a basic client instance
                SymphonyClient symClient = SymphonyClientFactory.getClient(SymphonyClientFactory.TYPE.V4, symphonyClientConfig);


            } catch (InitException e) {
                logger.error("error", e);
            }


The client will automatically generate the tokens necessary to access both the POD and Agent Server.  If you need access to these tokens, there is an object called **SymAuth** which holds both the session and key manager tokens.  You can retrieve this object through the client:

    SymAuth symAuth = symClient.getSymAuth();




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