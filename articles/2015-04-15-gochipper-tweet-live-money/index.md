
# #Chipper: Tweet Money Live

[Periscope](https://www.periscope.tv/) and [Meerkat](http://meerkatapp.co/) have brought live streaming to the masses. [Mukbang stars in Korea](http://www.allkpop.com/buzz/2014/09/top-8-mukbang-online-streamers-on-afreecatv) and [karaoke stars in China](http://qz.com/206694/chinas-tech-firms-are-counting-on-single-men-to-watch-more-live-karaoke-online/) are already making a living via financial transactions over live streaming services (such as [Afreeca](http://afreeca.com/)), but Periscope and Meerkat don't have this feature.. yet.

Since Meerkat's commenting system relies on Twitter (you basically @ the broadcaster in a tweet when you chat in the Meerkat broadcast), I thought it would be neat to build a way to pay someone with just a tweet. Basically, both the recipient and sender _must_ be authenticated with a payment solution (I went with [Venmo](https://developer.venmo.com/) due to its ubiquity in the consumer payments space) and provide their twitter handles. Then, any tweet containing `#chipper`, `$X` ('X' being a number), and `@` someone, will take money from the sender's Venmo account and put it into that of the recipient.

In the future, instead of just accepting Venmo, #Chipper would also allow people to sign up with a credit card (via [Stripe connect](https://stripe.com/connect)).

[Demo](http://www.gochipper.com).