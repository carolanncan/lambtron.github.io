
# Twapchat: Ephemeral Tweeting with Twilio Picture Messaging and Twitter

Send pictures to a Twilio phone number, which posts it to Twitter. First 10 people will then get to view the picture before it self-destructs.

## How to use

- Text a picture to a (240) 565-0664
- [@twapchat](https://twitter.com/twapchat) tweets the picture
- picture only gets 10 views before it self destructs

## How does it work

- [Twilio](https://www.twilio.com) receives picture and sends a `POST` request to a specified endpoint with picture data
- picture is saved to MongoDB
- URL is generated and hosted on server
- [@twapchat](https://twitter.com/twapchat) tweets the URL
- the server tracks the number of requests per URL; if 10 views then delete in Mongo, destroy URL, remove tweet

[Demo](http://twapchat.herokuapp.com/). [Source](https://github.com/lambtron/twapchat).

*Like what you read? Share this article and follow me on [Twitter](http://www.twitter.com/andyjiang) for random musings.*