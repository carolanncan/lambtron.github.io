
# Metalsmith-Hover: A Plugin to Show Gifs on Hover

I have some [exciting news](http://media.giphy.com/media/s7PYWohoEXPCo/giphy.gif) to share—not only did I write my first [Metalsmith](https://www.metalsmith.io) plugin, but also it's live on my blog! [Wewt](http://www.reactiongifs.com/r/dnc.gif)! I'll discuss how I went about doing so in this [post](http://i.imgur.com/hRxquuu.jpg), so come for the [arguably trivial insights](https://38.media.tumblr.com/cb6da16d6949f8fc6a363b40538f7e49/tumblr_inline_nctv9c7ODq1sv49sn.gif) and stay for the [mediocre](http://replygif.net/i/343.gif) [gifs](http://i.imgur.com/KpuscdT.gif)!

Inspired by such Chrome extensions such as [Hover Zoom](https://chrome.google.com/webstore/detail/hover-zoom/nonjdcjchghhkdoolnlbekcfllmednbl?hl=en), [ezLinkPreview](https://chrome.google.com/webstore/detail/ezlinkpreview/nnkcfbiefgdaceeplickkkmifpicbpcc?hl=en), [Hover Link](https://chrome.google.com/webstore/detail/hover-link/ggbpdjpocpnohglmhmppdkimpoiklegb?hl=en-US), [Link Previews](https://chrome.google.com/webstore/detail/link-previews/hlbhbhdjmllabhmeoehogilodnpbmhgj?hl=en), [Hover Free](https://chrome.google.com/webstore/detail/hover-free/hcmnnggnaofmhflgomfjfbndngdoogkj?hl=en), and [hundreds more](https://www.google.com/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=chrome%20extension%20hover%20link)—tools to drive all productivity on the Internet to a [mind numbingly infinite scroll](http://33.media.tumblr.com/8b0e3cbcfc4bdfbdad8320584128340c/tumblr_mmoodrLNAb1spes4io1_500.gif) fest of [cat](http://i.imgur.com/pz1C9fb.jpg) [gifs](https://38.media.tumblr.com/9ec0aeed0ef6f914d0daa05beb641648/tumblr_ndp649VVhG1s60v4go1_500.gif)—I decided that [bake](http://media.giphy.com/media/GT9BB2Zm4VnI4/giphy.gif) that right into my blog. Doing so would serve [two purpoises](http://i.imgur.com/SBduuXe.jpg): 1) allow me to further [feed](http://media.giphy.com/media/xT7aLRxSYZkk0/giphy.gif) my [gif addiction](http://media.giphy.com/media/4zwwZI3aSZgAg/giphy.gif), and 2) spread my [love of gifs](http://i.imgur.com/QTvZAIO.gif) to my handful of [devoted readers](http://media.giphy.com/media/tpdtC6yLhvhgA/giphy.gif).

This blog is built with [Plato](http://i.imgur.com/liYrWZw.jpg) (a currently closed source [Segment](https://www.segment.com/) project), which converts markdown files into a static site using [Metalsmith](https://www.metalsmith.io) (another Segment project, but this one is open source!). Plato uses various Metalsmith plugins, so I knew that in order to add this hover link ability, I would first need to write a plugin for Metalsmith and then add that to [Plato](http://i.imgur.com/QWfiTHO.jpg).

## Building Building Blocks To Build More Building Blocks

> A plug-in is a software component that adds a specific feature to an existing software application.

Metalsmith itself is really just an abstraction for [transforming](http://media.giphy.com/media/EreFiaDJ9zK6Y/giphy.gif) a directory of files. It takes a set of [files](http://i.imgur.com/pMF6W7y.gif) in the source directory, [manipulates each of them](http://i.imgur.com/lgCDCa0.gif) with the plugins, and then [outputs the files](http://media.giphy.com/media/AWicoNvwT3xAI/giphy.gif) in the destination directory. It is the rich ecosystem of these plugins that make Metalsmith as powerful as it is.

There are plugins for [converting Markdown to HTML](https://www.github.com/segmentio/metalsmith-markdown), [formatting your HTML, CSS, or JS files](https://github.com/boushley/metalsmith-beautify), [rendering any file through a templating engine of your choice](https://github.com/segmentio/metalsmith-templates), [etc](http://www.metalsmith.io/#the-plugins).

And not surprisingly, this is certainly inline with [Segment's approach](https://segment.com/blog/building-building-blocks/) to building software in [modular, pluggable components](http://i.imgur.com/Kh2Osoy.gif).

## What Happened Next Will Make You Continue To Sit (or Stand) There

This is my first Metalsmith plugin! Basically, this plugin would

- take an HTML file
- find all anchor tags where the `href` were images
- append a hidden `img` tag that would reveal itself upon hover
- prepend the necessary CSS to attach style to the `a:hover` pseudo class

I decided to use [Cheerio](https://github.com/cheeriojs/cheerio) for HTML/[DOM](http://i.imgur.com/i9qbCIX.png) querying, just because I am most familiar with its API (I've used it in previous projects with [great success](http://i.imgur.com/kpJspRB.png)).

Here is the [meat of the logic](https://github.com/lambtron/metalsmith-hover/blob/master/lib/index.js#L34-L44).

```javascript
function plugin() {
  return function(files, metalsmith, done) {
    Object.keys(files).forEach(function(file) {
      if (!~file.indexOf('.html')) return;
      var $ = cheerio.load(files[file].contents);
      var links = $('a');
      for (var i = links.length - 1; i >= 0; i--) {
        var url = links[i].attribs.href;
        if (image(url)) $(links[i]).append(img(url));
      }
      $.root().prepend(css());
      files[file].contents = new Buffer($.html());
    });
    done();
  };
}

```

*I'm not 100% sure if this is the best way to do this, so if you have any suggestions, please don't hesitate to add an issue [here](https://github.com/lambtron/metalsmith-hover/issues/new)!*

One interesting thing to point out is that [you are not able to add inline styles to the `a:hover` pseudo class](http://stackoverflow.com/questions/1033156/how-to-write-ahover-in-inline-css). It is possible to use javascript and change the styles of the anchor element with `onMouseOver` and `onMouseOut` functions, but, for this [wiener of a plugin](http://media.giphy.com/media/2aFRDAaACkSXu/giphy.gif), it seemed to be too much.

Instead, I [prepended a `style` element](https://github.com/lambtron/metalsmith-hover/blob/master/lib/index.js#L42) with [the `a:hover` css code](https://github.com/lambtron/metalsmith-hover/blob/master/lib/index.js#L71-L77) to the root of the file's HTML, which works. And yes, that is a [`z-index: 99`](http://i.imgur.com/3R8DKRa.jpg) in my CSS code. Hey, I've made it this far in life so I must be doing something right, [right](http://media.giphy.com/media/Gvnh1m87wk2ek/giphy.gif)? [Right](http://media.giphy.com/media/NsJs9nmLwAxag/giphy.gif)?!

[Anyway](http://media.giphy.com/media/8ppnU9unXdula/giphy.gif), the [plugin is now available](https://github.com/lambtron/metalsmith-hover) for any brave soul to use. I only say that because, like *everything* I release into the unforgiving conditions of the wild, it has [no tests](http://media.giphy.com/media/fhWTD7YCd59D2/giphy.gif) (but hey, [it works!](http://www.topito.com/wp-content/uploads/2013/01/code-08.gif)).

Install it like:

```shell
$ npm install metalsmith-hover
```

And use it like (assuming the source directory has Markdown files, which I hope is true for all of you writers out there!):

```javascript
var Metalsmith = require('metalsmith');
var markdown = require('metalsmith-markdown');
var hover = require('./lib/index.js');

var metalsmith = Metalsmith(__dirname)
  .use(markdown())
  .use(hover())
  .build(function(err) {
    if (err) throw err;
  });

```

## Gif the People What They Want

Now that I have this great plugin, how do I get it onto this wonderful blog so that I can save you a click to view [these](http://ak-hdl.buzzfed.com/static/2013-11/enhanced/webdr01/21/11/anigif_enhanced-buzz-25559-1385050824-30.gif) [awesome](http://ak-hdl.buzzfed.com/static/2013-11/enhanced/webdr02/21/12/anigif_enhanced-buzz-25961-1385055442-5.gif) [gifs](http://ak-hdl.buzzfed.com/static/2013-11/enhanced/webdr03/21/11/anigif_enhanced-buzz-27147-1385051681-11.gif)?

Plato, which is used to turn a series of [poorly formed thoughts](http://media.giphy.com/media/1bWDLvRtlhgk0/giphy.gif) hastily thrown into a collection of Markdown files into [this](https://github.com/lambtron/lambtron.github.io), a beautiful static-site that is hosted on [GitHub](https://www.github.com), has a `plugins.js` file. This file is where you can add whatever Metalsmith plugin you would like.

I added `metalsmith-hover`, saved the blog, and [deployed it](http://media.giphy.com/media/Lmhk4bVpRn4YM/giphy.gif). [Boom](http://i.imgur.com/kEgvj.gif)!

It's all about the first rule of journalism: [give the people what they want](http://i.imgur.com/Vca79Bt.gif)!

I hope you enjoyed this! [Tweet at me](https://www.twitter.com/andyjiang) if you have any [questions](http://media.giphy.com/media/MJTOHmGiGPHgI/giphy.gif), [comments](http://media.giphy.com/media/Bt7yp8KpygqYg/giphy.gif), or just [want to chat](http://media.giphy.com/media/oGfEeawH6DrZ6/giphy.gif).

*Like what you read? Share this article and follow me on [Twitter](http://www.twitter.com/andyjiang) for random musings.*
