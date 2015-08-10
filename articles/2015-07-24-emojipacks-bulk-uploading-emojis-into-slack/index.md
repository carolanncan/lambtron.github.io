
# ⚡️ EmojiPacks 🎒 : Bulk Uploading 🌟Emojis🌟 into Slack

We can all indisputably agree on that the three best things on 🌎 are warm blankets, emojis, and [Slack](https://www.slack.com). And when I discovered that I could add my own custom <img width=22px src='http://i.imgur.com/o7tyjxN.gif' style='display: inline; margin: 0' />, <img width=22px src='http://i.imgur.com/8lLklPJ.gif' style='display: inline; margin: 0' />, and <img width=22px src='http://i.imgur.com/GmcaGmz.gif' style='display: inline; margin: 0' /> to <img width=22px src='http://i.imgur.com/J9yDWph.png' style='display: inline; margin: 0' />, it was arguably one of the 🎉 😍 moments of my short-lived life (barely exceeding a close number two, when I discovered [this warm blanket](http://www.cafepress.com/+emojis_throw_blanket,1608034030)). So I built [emojipacks](https://www.github.com/lambtron/emojipacks), a command line interface, to bulk upload emojis into Slack. Let's take a 👀 at the code that accomplishes this ✨🌟✨🌟 feat.

## 👶 The Beginning

Shortly after I discovered the ability to add custom emojis in Slack, I was hooked like a 🐟 . I 🍩 even remember taking a break until the first 💯 <img height=22px src='http://i.imgur.com/yrd45Ky.gif' style='display: inline; margin: 0' /> emojis were added. But 🚢 emoji after emoji inevitably ate away at my entire day at work. Though I managed to enter a rhythm of 🔦 for emojis and then uploading them all at once, it still took a long ⌚️. I had to 🚧 off ⌛ ️on my 📅 just so I was able to have uninterrupted time dedicated to uploading emojis. Not the most glamorous of jobs, but somebody had to do it 🙌 💪 .

Knowing that <img width=22px src='http://i.imgur.com/J9yDWph.png' style='display: inline; margin: 0' /> is active on <img width=22px src='http://i.imgur.com/Y8DaHZx.png' style='display: inline; margin: 0' /> (they even have a [@SlackLovesTweets](https://twitter.com/slacklovetweets) account that just RT's awesome tweets), I decided to 🚀 this tweet to [@Slack](https://www.twitter.com/slack):

<blockquote class="twitter-tweet" lang="en"><p lang="en" dir="ltr"><a href="https://twitter.com/SlackHQ">@SlackHQ</a> Hi! We use slack here and love it. Just wondering if you thought about a way to bulk import custom emoticons? :)</p>&mdash; Andy Jiang (@andyjiang) <a href="https://twitter.com/andyjiang/status/542789755293814784">December 10, 2014</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

They gave me a 💬, but with 💩 😞 📰:

<blockquote data-conversation="none" class="twitter-tweet" lang="en"><p lang="en" dir="ltr"><a href="https://twitter.com/andyjiang">@andyjiang</a> No way to bulk import emoticons right now but we&#39;ll share the idea with the team! 📮</p>&mdash; Slack (@SlackHQ) <a href="https://twitter.com/SlackHQ/status/543006206521262080">December 11, 2014</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

So they have a 👔💼 to 🏃 and maybe there are way more important things on their product road map 🙊. *Maybe.* 

Fortunately for me, the Internet is made of a series of tubes, so there is definitely a way to hack together a solution to accomplish this.

## 💥 Emoji Packs

A few, long 🌑 🌓 🌕 🌗 🌚 of ✋ uploading one-off emojis pass. Until one 🌞, I came across [this awesome project](https://github.com/mootcycle/slackporter) on [Hacker News](https://news.ycombinator.com/) and was reinspired to 🔨🔧 something to bulk upload emojis.

[Slackporter](https://github.com/mootcycle/slackporter) allows you to transfer emojis from one Slack account to another. Super neat. Plus the [developer](https://twitter.com/mootcycle) is responsive on Twitter (super nice guy, too)!

I decided to re-use these 🔑 pieces: ✊ the user login information, logging in, and submitting the emoji form. I also re-wrote it with [Koa](http://koajs.com/) and [generators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators). The result is [emojipacks](https://www.github.com/lambtron/emojipacks)!

## 🔩 How this works

The script uses [Cheerio](https://github.com/cheeriojs/cheerio) for server-side HTML parsing and manipulation and [Request](https://github.com/request/request) to submit form requests.

The first step is to get to this 💻:

![](http://i.imgur.com/qUVR6Ko.png)

Then, the script grabs some 👻 form data, such as the sign in value, redirect path, and a unique session crumb. This 🔮 is used later to log into Slack.

![](https://cldup.com/BO7qWgzUcF.png)

With the form data, we can login to Slack:

![](http://i.imgur.com/AV9Wj5u.png)

*Note that this won't ever load, since it is a static HTML file.*

After doing so, we can go to the emoji upload page:

![](http://i.imgur.com/5rFpsVM.png)

Here, we grab an upload crumb from an input field. We'll use this crumb when programmatically submitting the form submit request.

## 🔍 Debugging

To help me understand which HTML page the script was on, I 📝 the Cheerio-generated HTML to `'./test/<title>.html'` files. Then I could open it up and see where I ended up in the upload emoji flow.

For example, I would get the HTML on the server, parse it with Cheerio, then ️💾 it to a 🏡 📁:

```javascript
var res = yield request(load);
var $ = cheerio.load(res[0].body);
write($('title').text(), $.html());
```

The function `write` is defined as:

```javascript
function(title, html) {
  var test = resolve(__dirname, '../test/' + title + '.html');
  fs.writeFileSync(test, html);
};
```

After 🏃 the script, I can go into the `./test` directory, use this nifty dotfile command `server`* to spin up a local server in that directory, and check it out with my browser.

```bash
  🍉 🍉 🍉  emojipacks-cli (master): cd test
  🍉 🍉 🍉  test (master): server
Serving HTTP on 0.0.0.0 port 4000 ...
127.0.0.1 - - [25/Jul/2015 08:26:38] "GET / HTTP/1.1" 200 -
127.0.0.1 - - [25/Jul/2015 08:26:40] "GET /Slack.html HTTP/1.1" 200 -
```

Then you can browse to see where the script was able to navigate.

![](http://i.imgur.com/utp1BQ3.png)

*The super convenient `server` command is taken from my mate Anthony's [dotfiles](https://github.com/anthonyshort/dotfiles/blob/master/bash/functions).

## 🚀 Next

So there you have it. The only thing next is to make more emoji 📦🎁 and try to find the upper limit in the number of custom emojis you can have in Slack.

🌊 you doing today? If you would like to contribute, please [suggest an emoji pack](https://20p.typeform.com/to/xOFDyq)!

[Source](https://www.github.com/lambtron/emojipacks). [Demo](https://www.emojipacks.com).

*Like what you read? Share this article and follow me on [Twitter](http://www.twitter.com/andyjiang) for random musings.*