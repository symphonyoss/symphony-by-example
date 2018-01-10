---
nav-sort: 112
nav-level: 1
---

# Understanding Streams

All conversations within Symphony are identified under a unique identification called a **stream**.

You need to understand this concept in order to establish a chat or room session with the Symphony system.

Streams are unique ASCII strings that represent a specific conversation.

Example:

    87P7fNs9_ndGVCWtG1HF5X___qv_yK0KdA

## One to One and Multi-party conversations

Stream identifiers are also unique to the number of users in a one to one or multi-party conversation.
For instance a conversation with userA and userB would have a unique stream identifier streamA.  If we wanted to make this a multi-party conversation and add in userC (A+B+C), then the stream would be different as in streamB.  In other words, there is no way to promote a one-to-one conversation to a multi-party and retain the same stream.

## Chat rooms

Chat rooms (public or private) are also identifiable as a stream, but do support the ability to add and remove users to the conversation.  Chat rooms are backed by an actual application service within Symphony which is managing the access entitlements and general room properties.


## Identifying a stream using the Symphony Desktop or Admin Console

The fastest way to identify a stream for a given conversation or room is to use the Symphony desktop.  Click on any timestamp on any given line within the conversation and locate the conversation ID on the bottom of the window.

    Conversation ID: 3zdMK7xJo/Ud30PIaQHecX///qpADfOxdA==

The conversation ID can be converted to a stream ID by replacing all forward slashes to underscore and removing equal signs.

    Converted stream ID: 3zdMK7xJo_Ud30PIaQHecX___qpADfOxdA

Optionally for chat rooms, you can use the Admin console to search for rooms and display conversation identifiers.


## Retrieving 1:1 or Multi-party stream with the SJC

A stream can be easily obtained using the **StreamsClient** as part of the SJC.  This is required if you are going to send messages without using any of the higher level services (ChatService, RoomService) offered in the SJC.  Either a SymUser(s) object or email address is required.

Note: SymUser objects only need the "id" to be populated in the object for lookups to work.


      //From Email
      SymStream stream = symClient.getStreamsClient().getStreamFromEmail("first.last@company.com");

      //From SymUser
      SymUser symUser = new SymUser();
      symUser.setId(12345678);
      Stream stream = symClient2.getStreamsClient().getStream(symUser);

      //From Set<SymUser>
      Set<SymUser> symUsers = new HashSet<>();
      symUsers.add(aSymUser);
      symUsers.add(bSymUser);
      Stream stream = symClient2.getStreamsClient().getStream(symUsers);



