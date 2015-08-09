
# Publish to Blog via Slack

Because I want the future of accomplishing _anything_ with <img width=22px src='http://i.imgur.com/J9yDWph.png' style='display: inline; margin: 0' /> to be here now. Here is how you can setup a blog (hosted by [Dropbox](https://www.dropbox.com/)) and update it by sending a message in [Slack](https://www.slack.com).

## Pancake.io

[Pancake.io](https://pancake.io) (definitely one of the cooler domains I've seen) allows you to host a blog with static Dropbox files. There are [some others](http://webdesign.tutsplus.com/articles/7-dropbox-powered-site-creation-platforms--cms-21231) out there, but this one is free and only takes two minutes to setup.

Register, create a new project, and authenticate your [Dropbox account](https://www.dropbox.com/). Pancake will then create a new directory in your Dropbox, `/App/Pancake.io`, with some default files. Note that this is where you would put new files for your blog.

![](http://i.imgur.com/hrk3GCk.png)

You can check out your beautiful creation immediately, as well. Just point your browser to the customizable subdomain Pancake provides you:

![](http://i.imgur.com/1s4fRRL.png)

Voila! Now you can't say that you didn't do something today. Good on you.

## Zapier

A personal favorite, [Zapier](https://www.zapier.com) let's you tie together all sorts of web services to make [some pretty nifty things](https://www.google.com/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=best%20zaps). Let's setup Zapier so that every new message in a Slack channel will be saved to the `/App/Pancake.io` directory in Dropbox.

These steps are pretty self-explanatory:

![](http://i.imgur.com/xdbpK7s.png)

For the Slack filter, I decided to create a new channel called `#blog` (totally up to you, you are the captain of your own ship):

![](http://i.imgur.com/qiVLYE9.png)

Finally, for each new message in Slack that match the previous filter, tell Zapier to create a file in the right directory with the right information. I chose to use `timestamp` as the filename, but again, up to you.

![](http://i.imgur.com/v4pF1ua.png)

Then, save and enable!

## Blog

[Here](http://andy-slack.pancakeapps.com/1439137541.000007.txt) is _this_ post published via Slack to my Pancake blog (yes, I did just publish this entire markdown file in `#blog`)!

A few words of warning about this egregiously stitched together hack:
- Pancake doesn't easily provide you with an archive of existing blog posts from your blog's home page. You would need to explicitly link your blog posts together like a 1999 [web ring](https://en.wikipedia.org/wiki/Webring). On a positive note, this could be an opportunity to be ahead of the curve when these regain popularity.
- You're unable to edit the blog post-publish via Slack. Sorry everyone, I'm just a dude who likes to surf the inter webs, not some magician.
- Looks like Pancake hasn't tweeted since 2014. Let's hope they're just heads down cooking up something awesome!!

If you are able to find work arounds to either of the above points, please let me know!

Hope you found this useless and distracting! And if you're not having fun, then you're not doing it right!!

*Like what you read? Share this article and follow me on [Twitter](http://www.twitter.com/andyjiang) for random musings.*