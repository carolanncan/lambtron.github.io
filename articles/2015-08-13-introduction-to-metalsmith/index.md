
# Introduction to Metalsmith

When I first encountered [Metalsmith](https://www.metalsmith.io), I thought that it was similar to [Jekyll](http://jekyllrb.com/). It wasn't until I had worked on two to three projects using Metalsmith that I fully realized its flexibility and power. This post is an introduction to Metalsmith and its extensibility.

## What is Metalsmith?

The [Metalsmith website](https://www.metalsmith.io) has a good description of what it does:

> We keep referring to Metalsmith as a “static site generator”, but it’s a lot more than that. Since everything is a plugin, the core library is actually just an abstraction for manipulating a directory of files.

If [Rails](http://rubyonrails.org/) is an opinionated approach to designing and building web applications, [Jekyll](http://jekyllrb.com/) is the same for building blogs and [Middleman](https://middlemanapp.com/) is the same for building static sites. Conversely, [Express](http://expressjs.com/) / [Koa](http://koajs.com/) are _unopinionated_ in their approaches, as is Metalsmith to static sites.

> The term '[opinionated](http://stackoverflow.com/questions/802050/what-is-opinionated-software)' suggests how many software design decisions have already been decided for you.

Metalsmith, at its core, takes files in a source directory, perform operations on them via plugins, and writes those files into a destination directory. Plugins can do virtually anything: create new files, filter out files, modify files based on some logic, etc (note this means that plugin order _does_ matter; transformations done by one plugin can be seen and used by following plugins, making the process a modular build pipeline). The entire [ecosystem of plugins](https://github.com/segmentio/metalsmith.io/blob/master/src/plugins.json) is what makes Metalsmith so flexible.

*Note that you have control over telling Metalsmith the location of the source and destination directories.*

## How does Metalsmith (and its plugins) work?

For example, let's see what Metalsmith does if there are no plugins. Here is the directory (`./node_modules` is hidden):

```
.
├── src
|   ├── index.md
|   └── nested
|       └── index.md
└── index.js
```

Here is `index.js` that we'll be running (you can also run Metalsmith via CLI, more on that later):

```javascript
var Metalsmith = require('metalsmith');

Metalsmith(__dirname)
  .build(function(err) {
    if (err) throw err;
  });
```

Here is what the `./build` directory would look like:

```
.
├── src
|   ├── index.md
|   └── nested
|       └── index.md
└── index.js
```

..nothing has changed (not even the text inside the markdown files). This is expected since no plugins (i.e. transforms) are being performed.

Let's dig one level deeper and see what Metalsmith is seeing to help us build a better picture of what's going on. We'll add this really simple function (hint: it's a plugin!) that just logs some info:

```javascript
var Metalsmith = require('metalsmith');

Metalsmith(__dirname)

  // Your first plugin! Wewt!
  .use(function(files, metalsmith, done) {
    console.log('Files: ');
    console.log(files);
    console.log();
    console.log('Metalsmith: ');
    console.log(metalsmith);
  })

  .build(function(err) {
    if (err) throw err;
  });
```

Here is the output:

```json
Files:
{ 'index.md':
   { contents: <Buffer 0a 48 65 6c 6c 6f 2c 20 77 6f 72 6c 64 21>,
     mode: '0644',
     stats: { ... } },
  'nested/index.md':
   { contents: <Buffer 0a 49 27 6d 20 69 6e 20 61 20 6e 65 73 74 65 64 20 66 6f 6c 64 65 72 21>,
     mode: '0644',
     stats: { ... } } }

Metalsmith:
{ plugins: [ [Function] ],
  ignores: [],
  _directory: '/Users/andyjiang/Developer/metalsmith-example',
  _metadata: {},
  _source: 'src',
  _destination: 'build',
  _concurrency: Infinity,
  _clean: true,
  _frontmatter: true }
```

> Yes, there is a ton of junk in `files`! Just note that the property `contents` in each file is the only important thing, you can ignore everything else.

We see that the argument `files` in our function is an object map of all the files in the source directory (and that `files[PATH].contents` is the body of the file). Many plugins will iterate through `files` and perform operations on them.

Also, we see that the second argument, `metalsmith`, contains some metadata: plugins, directory, even an explicit object for metadata, etc. This is important, because plugins can store config or other information in metadata, or access the metadata to help with file operations.

The last argument, `done`, is a function that you can call to tell Metalsmith to go to the next plugin in its queue (or `.build()`, if it's the last one).

### Front-matter Makes it Easy

Front-matter is [YAML]() that can precede _any_ file (sandwiched by two `---`). Metalsmith will automatically convert this to a key-value in the `file`'s object. For example, the below in `./src/index.md`:

```yaml
---
title: "Teach me how to dougie"
draft: false
foo: bar
---

Hello, world!
```

Will become this (we're just `console.log()`ing the `file`):

```json
{ 'index.md':
   { title: 'Teach me how to dougie',
     draft: false,
     foo: 'bar',
     contents: <Buffer 0a 0a 48 65 6c 6c 6f 2c 20 77 6f 72 6c 64 21>,
     mode: '0644',
     stats:
      { ... } }
```

We now have `title`, `draft`, and `foo` available for our plugins to use. Many plugins rely on the front-matter to easily transform files, such as filtering out files with `draft: true` or organizing files into different collections (e.g. `collection: articles`).

Here are some example plugins that you can check out to see how they're manipulating the `files` and `metalsmith` arguments.
- [metalsmith-drafts](https://github.com/segmentio/metalsmith-drafts/blob/master/lib/index.js)
- [metalsmith-metadata](https://github.com/segmentio/metalsmith-metadata/blob/master/lib/index.js)
- [metalsmith-permalinks](https://github.com/segmentio/metalsmith-permalinks/blob/master/lib/index.js)
- [metalsmith-excerpts](https://github.com/segmentio/metalsmith-excerpts/blob/master/lib/index.js)

## How do I use Metalsmith?

Because Metalsmith relies on its plugins to be useful, the options for plugins to use can be overwhelming.

> [With great power, comes great responsibility.](http://i.imgur.com/mP5YIkM.jpg)
> 
> *Uncle Ben*

If you are building a blog generator, then here are some plugins (in order) that may help you get started (in fact, these are what we use to build the [Segment blog](https://segment.com/blog/)):
- [build date](https://github.com/segmentio/metalsmith-build-date): add a global `date` property to the Metalsmith metadata that can be used later (like in the atom feed)
- [drafts](https://github.com/segmentio/metalsmith-drafts): delete any file that has `draft: true` in the front-matter
- [excerpt](https://github.com/segmentio/metalsmith-excerpt): extract the first paragraph of each file into `file.excerpt`
- [collections](https://github.com/segmentio/metalsmith-collections): group files by category (this is a neat plugin that will find all files in the same category and connect them; it'll add the second file as a property `next` of the first file, etc.)
- [markdown](https://github.com/segmentio/metalsmith-markdown): turn markdown into HTML by replacing the `file.contents` with HTML (using [marked](https://github.com/chjj/marked))
- a custom plugin that saves the `contents` to `body` so it can be used in the atom feed (later, `contents` is going to be merged with the layouts plugin)
- a custom plugin that attaches author metadata to each file (uses an internal web service that returns team members at Segment)
- a custom plugin that attaches category metadata to each post
- a custom plugin that attaches related posts to each post; it adds a `related` property whose value is an array of other posts to each post 
- [permalinks](https://github.com/segmentio/metalsmith-permalinks): add `path` object to each `file` that will serve as the URL
- [only-build](https://github.com/segmentio/metalsmith-only-build): delete any reference from the `files` hash that is not specified to be built (for performance)
- [templates](https://github.com/segmentio/metalsmith-templates): pass each file in the `files` hash through the template denoted `file.template`, so that the written output in the destination file is the final HTML (templates is deprecated and split into two smaller plugins—[metalsmith-layouts](https://github.com/superwolff/metalsmith-layouts) and [metalsmith-in-place](https://github.com/superwolff/metalsmith-in-place))

Still with me? Thats a lot to take in. And on top of that, some of the above plugins require specific things to include in front-matter or in variables set in the environment to work properly.

Aside from blog generators (of which you could make tons of different ones with different plugins), here are some other ideas for which you can use Metalsmith:
- project scaffolder
- a build tool (convert, concatenate, and minifi CSS files)
- an ebook generator
- technical docs builder

[More examples](https://github.com/segmentio/metalsmith/tree/master/examples).

## Conclusion

I hope this helps clarify a few things and gives you enough information to get deeper in Metalsmith! Here are some further resources to help you out:

[Rebuilding Keetology](http://keetology.com/blog/rebuilding-keetology)

[Way of Metalsmith](http://blog.lecomte.me/posts/2014/way-of-metalsmith/)

[Static is The New Dynamic](http://www.thenewdynamic.org/)

[Building a Blog with Metalsmith](http://www.okaythree.com/2015/03/building-a-blog-with-metalsmith/)

[Building a Blog with Metalsmith](https://azurelogic.com/posts/building-a-blog-with-metalsmith/)

Have fun!

*Like what you read? Share this article and follow me on [Twitter](http://www.twitter.com/andyjiang) for random musings.*
