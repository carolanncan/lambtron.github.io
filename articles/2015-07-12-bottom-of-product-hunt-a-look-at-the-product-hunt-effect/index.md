
# BottomOfProductHunt: A Look Into the "Product Hunt" Effect

[Product Hunt](https://www.producthunt.com) has become the de facto way to launch and grow your business. I've recently had the unique opportunity to have something featured on Product Hunt. Let's take a look at the traffic numbers behind the fabled "Product Hunt" effect.

On June 20th, 2015, I launched [BottomOfProductHunt](http://www.producthunt.com/posts/bottom-of-product-hunt) on Product Hunt.

[BottomOfProductHunt](http://www.bottomofproducthunt.com) is a single purpose, zero-value-add website that only shows posts on Product Hunt beneath a certain score. It was built as a fun side project, primarily to troll [my friend](https://twitter.com/dirtyanalytics), who is the project's inspiration and muse. As such, I had zero (arguably, negative) expectations about how this site would perform post-Product Hunt launch. Knowing the quirks of Silicon Valley, however, it also would not be out of the question if something like this ends up raising a round of financing, or whatever.

Like any data-minded, analytics-driven troll, I wanted to know _just_ how far reaching my half-baked efforts extended. I added [Segment](https://www.segment.com)'s analytics.js snippet to the page and turned on [Google Analytics](https://analytics.google.com). Since this was essentially a static site, I did not map out any "events" that I wanted to track, so the js snippet alone was enough to track page views.

## Do you even lift, bro?

Here is a screenshot of the Google Analytics session report. For those of you sane enough to not get into the analytics business, a ["session"](https://support.google.com/analytics/answer/2731565?hl=en) is defined as a group of interactions that take place on your website within a given time frame. In our special case, it just means someone looking at the website.

![](http://i.imgur.com/yT1JvmC.png)

The first day of being on Product Hunt gave it a whopping 403 sessions, which is 401 more sessions than expected (one from me and one from [@DirtyAnalytics](https://twitter.com/dirtyanalytics)). At an average duration session of 21 seconds, that is ~141 collective minutes of human kind that cannot be returned [#sorrynotsorry](http://media.giphy.com/media/rd4PovI7PgYw0/giphy.gif).

Also, even after Product Hunt fanfare settled, I did not expect sustained traffic, or "lift". There are still daily consistent 20-50 sessions on the site. Though I suspect this is largely from robots, but I will certainly keep an eye on this for the next few months, before I begin putting together my pitch deck and approaching reputable VCs.

Being the scrappy growth hacker that I am, I decided to move my mouse cursor further down in the Google Analytics interface and click on "Acquisition" to learn more about where all of this undeserved traffic came from.

## Referrer Madness

Here is a screenshot of the GA Acquisition > All Traffic > Source/Medium report.

![](http://i.imgur.com/YiQmOYH.png)

Again, for those smart enough to avoid the analytics industry, a "referrer" is the URL of the previous webpage from which the link was followed. This is automatically done by HTTP requests (in the header), but used widely by analytics tools.

Obviously, the largest bulk of the traffic came from [producthunt.com](https://www.producthunt.com) as a referrer. The second majority is "direct", meaning that people visited the site directly in their browser, which actually means that they clicked on a link sent from other people, who have achieved the "a-ha" moment from being on the bottomofproducthunt.com site and wanted to share it to their closest enemies as a way to derail their unwavering focus with additional meaningless noise.

All of the other referral sources are from the organic, human, certainly not robots "more-free-traffic" domains. It may be considered [referral](https://forums.digitalpoint.com/threads/fake-referral-traffic.2750838/) [spam](http://viget.com/advance/removing-referral-spam-from-google-analytics) to some, but it is this kind of mini affirmation that gets me out of bed in the mornings.

This [one](http://www.get-free-traffic-now.com/) is particularly amusing:

![](http://i.imgur.com/K2E69zv.jpg)

Another unusual one is actually [event-tracking.com](http://event-tracking.com/). I thought it would be another robot one, but it actually is a guide to using Google Analytics analytics.js library to send events. Good on you for being helpful.

Aside from the robots, I see a few [feedly](http://feedly.com/) users, [thenews.im](http://thenews.im/) users, [pulse.me](https://www.pulse.me/) users, [alltechnews.co](http://alltechnews.co/) users, and [Lost Hunts](http://losthunts.com/) users (Bottom of Product Hunt is pretty much a shittier, less-inspired clone of this one). Also a few referrers from Twitter (via this link, [http://t.co/kol9fbT9JJ](http://t.co/kol9fbT9JJ); unfortunately, Twitter won't show me what the original tweet is, but I'm guessing it's [this one](https://twitter.com/ProductHunt/status/612404808109174784)). [Yay, social](http://media.giphy.com/media/kViTiGaoqD0gU/giphy.gif)!

Some domains I did not recognize are [revery.io](http://revery.io/) ("updates from all your favorite sites", still in beta testing), [jianshu.com](http://www.jianshu.com/) (looks like a chinese news aggregator), and [netvibes.com](http://www.netvibes.com/en) ("a dashboard for everything", including probably news stories).

There is also one poor soul who came to the site from a Google search. I wonder what stray Google query led to that?

## What's next?

Probably nothing. Unless someone pokes me to do something about this site, it'll most likely end up lost in some dark corner of the web, where it'll find a highly devoted niche following in some random community. But I'll continue to invest in renewing the domain, just in case some astute, enterprising person wants to acquire the consistent stream of valuable bot traffic.

Hope this was useful for your Product Hunt launch!! If you have any questions or comments, please [tweet me](https://www.twitter.com/andyjiang)!

*Like what you read? Share this article and follow me on [Twitter](http://www.twitter.com/andyjiang) for random musings.*
