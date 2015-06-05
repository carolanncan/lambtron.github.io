
# Ladder: A simple Elo rating app

We take things very seriously here at Segment. But nothing is taken more seriously than our ping pong, which explains why we play on a non-Olympic regulation sized table (that sits snuggly above our junior-sized pool table), in addition to our sole use of [one star balls](http://sports.stackexchange.com/questions/69/why-are-there-different-stars-on-table-tennis-balls). In fact, the Ping Pong culture at Segment is _so_ strong, we even have a dedicated Slack channel for it.

![](http://i.imgur.com/fpXkYJ2.png)

But what fun is it to play Ping Pong with your co-worker without keeping track of everyone's skill level, especially in a low-friction, asynchronous way? Being the resident Derp Engineer, I knew it was mostly on me to solve this in a manner most obtrusive and obnoxious—by writing an app.

## Elo rating system

The [Elo rating system](http://en.wikipedia.org/wiki/Elo_rating_system) is a method for calculating the relative skill levels of players in head-to-head games such as chess. Currently, it has been adapted for use in many multiplayer video games and team sports, including football, basketball, baseball, competitive programming.

The basic principle is that the difference between two players ratings will serve as a predictor of the outcome of the match (two players with the same rating will be seen by the Elo system as having equal chances of winning). The winner of the match will take a proportional number of points with regards to the predicted outcome from the loser, i.e. if the player with the higher Elo rating going into the match wins, then she will take fewer points from the loser; if the player with the lower Elo rating wins, then that player will take more points.

I did a quick search on [NPM](https://www.npmjs.com/) (using the awesome [`npm`](https://github.com/willfarrell/alfred-workflows) [Alfred workflow](http://support.alfredapp.com/workflows) command) and came across this module, [elo-rank](https://www.npmjs.com/package/elo-rank/), that takes two players starting ratings, the outcome of the match, and returns the new ratings (example below).

```javascript
var elo = require('elo-rank');
 
var playerA = 1200;
var playerB = 1400;
 
//Gets expected score for first parameter 
var expectedScoreA = elo.getExpected(playerA, playerB);
var expectedScoreB = elo.getExpected(playerB, playerA);
 
//update score, 1 if won 0 if lost 
playerA = elo.updateRating(expectedScoreA, 1, playerA);
playerB = elo.updateRating(expectedScoreB, 0, playerB);
```

With this, I decided to throw together a derpy app!

## Ladder: NodeJS, MongoDB, Koa, Deku

There is no better time than working on side projects to experiment with new technologies, so I decided to experiment with [Segment](https://www.segment.com)'s [Deku](https://www.github.com/dekujs/deku), a functional alternative to [ReactJS](https://facebook.github.io/react/).

The front-end is just built with these [components](https://github.com/lambtron/ladder/tree/master/client/js). *A word of caution! This was built using `Deku v0.0.28`, an early beta version; most recommended patterns are probably much different now. To see examples of Deku in action, go [here](https://github.com/stevenmiller888/awesome-deku). To read about the thought process behind creating Deku, go [here](https://segment.com/blog/deku-our-functional-alternative-to-react/).*

For the server, I decided to use [Koa](https://github.com/koajs/koa) to set up a [few routes](https://github.com/lambtron/ladder/blob/master/server/lib/routes.js) to add and remove players, as well as submit game outcomes.

And here is the finished product:

![](http://i.imgur.com/wwtLBKV.png)

## Other Ping Pong hacks, apps, projects

It is no secret that any area with a high concentration of nerds, there will organically emerge some kick ass DIY projects around Ping Pong. And the Bay Area is no exception.

### [Campaign Monitor](https://www.campaignmonitor.com)

We really respect the team over at Campaign Monitor, not only for just being rad people, but also for taking their ping pong culture _so_ seriously that they actually spun out their in-house ping pong pet project into its own commercial product, [Ping Pong Ninja](http://www.pingpongninja.com/about/).

Read about their ascension into Ping Pong nirvana [here](https://www.campaignmonitor.com/blog/post/3179/we-take-ping-pong-seriously-around-these-parts/).

### [Si Digital](http://sidigital.co/)

Sure, tech-minded folks have been chastised for spending resources on solving problems for the 0.01% (you read correctly, the one percent of the one percent), such as [world famine](http://www.herecomestheairplane.co/) and [priviledge distribution](http://welldeserved.me/). I suspect its only because the public has overlooked the tremendous contributions of Si Digital to the Ping Pong world, using RFID tags in the players' bats to track them, tapping buttons under the table to indicate a point, and having giant display screens in the room to keep score.

Read about their unparalleled contributions to the Ping Pong world [here](http://sidigital.co/blog/lab-notes-hacking-our-ping-pong-table).

## Next Steps

We plan to hold 5 on 5 Ping Pong tournaments between companies! We just played [Sift Science](https://siftscience.com) a month ago (we lost 3-2) and it was a ton of fun!

![](http://i.imgur.com/70h0Wk2.jpg)

Want to challenge the Segment team to a match? Contact me [here](mailto:andy@segment.com?Subject=Ping%20Pong%20Match) and we'll get it setup!

[Source](https://www.github.com/lambtron/ladder). [Submit an issue](https://github.com/lambtron/ladder/issues).

*Like what you read? Share this article and follow me on [Twitter](http://www.twitter.com/andyjiang) for random musings.*