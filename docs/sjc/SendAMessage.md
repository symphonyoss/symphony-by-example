---
nav-sort: 113
nav-level: 1
---

# Send a message

The first thing you should attempt with the SJC is sending a simple message.

See: [SendMessage.java](https://github.com/symphonyoss/symphony-java-client/blob/master/examples/sendmessage/SendMessage.java)

```
package sendmessage;

import org.glassfish.jersey.logging.LoggingFeature;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.symphonyoss.client.SymphonyClient;
import org.symphonyoss.client.SymphonyClientConfig;
import org.symphonyoss.client.SymphonyClientConfigID;
import org.symphonyoss.client.SymphonyClientFactory;
import org.symphonyoss.client.exceptions.MessagesException;
import org.symphonyoss.client.exceptions.StreamsException;
import org.symphonyoss.client.util.MlMessageParser;
import org.symphonyoss.symphony.clients.model.*;


/**
 * Streams example showing how you can search for bot user associated streams by criteria filer.
 *
 *
 * REQUIRED VM Arguments or System Properties or using SymphonyClientConfig:
 * <p>
 * -Dtruststore.file=
 * -Dtruststore.password=password
 * -Dsessionauth.url=https://(hostname)/sessionauth
 * -Dkeyauth.url=https://(hostname)/keyauth
 * -Duser.call.home=frank.tarsillo@markit.com
 * -Duser.cert.password=password
 * -Duser.cert.file=bot.user2.p12
 * -Duser.email=bot.user2@domain.com
 * -Dpod.url=https://(pod host)/pod
 * -Dagent.url=https://(agent server host)/agent
 * -Dreceiver.email= email address of user or bot who will receive a message.
 *
 *
 *
 * @author Frank Tarsillo on 5/9/17.
 */
//NOSONAR
public class SendMessage {


    private final Logger logger = LoggerFactory.getLogger(SendMessage.class);


    private SymphonyClient symClient;

    public SendMessage() {


        init();


    }

    public static void main(String[] args) {

        new SendMessage();

    }

    public void init() {


        try {


            SymphonyClientConfig symphonyClientConfig = new SymphonyClientConfig();

            //Disable all real-time services
            symphonyClientConfig.set(SymphonyClientConfigID.DISABLE_SERVICES, "True");

            //Create an initialized client
            symClient = SymphonyClientFactory.getClient(
                    SymphonyClientFactory.TYPE.V4,symphonyClientConfig);


            //Email for the destination user
            String receiverEmail = symphonyClientConfig.get(SymphonyClientConfigID.RECEIVER_EMAIL);


            //Create a message
            SymMessage symMessage = new SymMessage();
            symMessage.setMessageText("Hello world...");


            try {

                //Lookup stream for destination user and send message.
                symClient.getMessagesClient().sendMessage(symClient.getStreamsClient().getStreamFromEmail(receiverEmail), symMessage);

            } catch (MessagesException e) {
                logger.error("Failed to send message",e);
            }



            if(symClient != null)
                symClient.shutdown();

            logger.info("Finished");



        } catch (StreamsException e) {
            logger.error("error", e);
        }

    }

}
```



