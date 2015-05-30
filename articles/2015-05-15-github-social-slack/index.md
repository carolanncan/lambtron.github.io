
# GitHub Social Slack: Posting GitHub User Events in Slack

Slack is awesome. So is GitHub. Also, GitHub's integration with Slack is neat, but only sends events on a given repo or organization. How cool would it be if we could send updates based on other users (when someone watches a new repo, forks or creates a repo, deploys something, etc.) into Slack?

The use case that inspired this is my side project of side project hackers, 20% Club. I wanted a passive and ambient way to send all of the coding activity into a Slack channel so we can see what new repos others are watching, forking, and creating. At first I was thinking of building an app and having each user auth with their GitHub account, but then realized the easier way would be just to create a new dummy user who just "follows" everyone's GitHub accounts. Then the dummy user's news feed would be exactly what would be posted into Slack.

Taking this:

![](https://camo.githubusercontent.com/ef6090465557faa52e02076e4684268c652ce047/687474703a2f2f692e696d6775722e636f6d2f694b69756765322e706e67)

..and posting it into Slack:

![](https://camo.githubusercontent.com/281e140794314d3adcba06a595a5590d828ccc34/687474703a2f2f692e696d6775722e636f6d2f34693866376b772e706e67)

Check the source code below to fork or deploy your own version!

[Source](https://github.com/lambtron/github-social-slack).

*Like what you read? Share this article and follow me on [Twitter](http://www.twitter.com/andyjiang) for random musings.*