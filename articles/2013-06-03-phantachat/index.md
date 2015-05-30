# Phantachat: NodeJS, WebSockets, and Ephemeral Conversation

In person conversation is fleeting. Words hang in the air for a brief moment then vanish suddenly. All participating members have to be present and aware in order to keep up with the conversation.

Conversation online has traditionally been very asynchronous. Instant message and email both log your messages with a timestamp, allowing you to maintain a history conversations for you to browse afterwards. You can message your friend knowing that she probably won’t respond immediately.

[Phantachat](http://phantachat.herokuapp.com) is an online chat web app, but tries to mimic chat in person. Your messages linger for no longer than a few seconds, forcing you to have that page open on your screen to be able to follow the conversation. Messages are also sent in immediate real time, without having you to hit enter, and there is no backspace.

## Websockets

Websocket is a web technology providing simultaneous two-way communications channels over a single TCP connection. This allows for immediate, seamless, synchronous data passing between the client and the server. We decided that in order to get the chat to behave as real time as possible, that we would choose to use Websockets (we went with Socket.io withNode.js/Express.js).

Unfortunately, there are very few free web/application hosting services out there that support websockets. Heroku doesn’t support web sockets, but you can configure your application to instead use long polling, which allows the client to send HTTP requests to the server at regular intervals and immediately receives a response.

Some other hosting platforms out there (www.appfog.com, http://www.dotcloud.com, http://www.nodejitsu.com) may support web sockets, but unfortunately they don’t provide a free tier. Our next plans are to spin up an instance on AWS so we can deploy a solution that uses websockets instead of long polling.

## Dynamic Chatrooms

In order to generate a unique chatroom, we created added some logic to the routing: if someone goes to the root, then generate a new hash (I used the hashids package for generating codes) and redirect to the new route with the hash at the end and start a new socket.io namespace; if someone goes to a hash that exists, then throw that person into the same socket.io namespace; and if someone goes to a route that is a hash that doesn’t exist, the server will return an error page.

With socket.io, there are two main ways of creating ‘chatrooms': using ‘rooms’ or using ‘namespacing’. We decided to use namespacing, because namespaces can be connected to by the client (but only if it already exists on the server). With rooms, they can only be joined on the server side.

When a new namespace is created (when a new hash is generated on the server), the server creates a new namespace, then passes the hash to the client to allow the client to join that specific namespace.

## Next Steps

This is still a very simple app. Next steps could be sharing other types of media that will also vanish within a few seconds (images, for instance). Or to allow more than two people to join each chat room (right now, it is only one on one).

Suggestions, feedback, and new ideas are always welcome!

[Demo](http://phantachat.herokuapp.com). [Source](https://github.com/lambtron/phantachat).

*Like what you read? Share this article and follow me on [Twitter](http://www.twitter.com/andyjiang) for random musings.*