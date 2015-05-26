
# Nightmare-LinkedIn: Crawling LinkedIn and Creating a LinkedIn API

LinkedIn in true LinkedIn fashion has shut off its API for developers to use. But that's ok—we all knew this day was coming! Regardless, the inter webs allows us additional alternatives to programmatically grab the data that we need.

[Nightmare](http://www.nightmarejs.org/) is a high-level wrapper for PhantomJS (created right here at [Segment](https://www.segment.com/)) to help us automate various web tasks that did not have an API. Nightmare not only makes it much easier to write and maintain code for virtual browsing, but also provides a platform to build [plugins](https://github.com/segmentio/nightmare#plugins) to repeat automated sequences in a single call.

[Nightmare-LinkedIn](https://github.com/lambtron/nightmare-linkedin) is a LinkedIn plugin that allows you to easily interface with LinkedIn's website. A few methods that are exposed include `.login()`, `.search()`, `.filter()`, `.crawl()`, and `.connect()`. Here is a quick example of how to use this plugin with Nightmare to login to your CEO's LinkedIn account and then view two pages the profiles of product managers in United States (with the intent that the users whom the bot has crawled will see your CEO viewing his/her profile):

```javascript

/**
 * Dependencies.
 */

var Nightmare = require('nightmare');
var LinkedIn = require('nightmare-linkedin');
var nightmare = new Nightmare();

/**
 * Set variables.
 */

var email = 'ceo@startup.com';
var password = 'selltoproductmanagers';
var type = 'people';
var query = 'product manager';
var filter = ['United States'];
var pages = 2;

/**
 * Set nightmare commands.
 */

nightmare

  // Login to LinkedIn.
  .use(LinkedIn.login(email, password))

  // Search for something and set the types of results.
  .use(LinkedIn.search(type, query))

  // Filter the search results for 'United States'.
  .use(LinkedIn.filter(filter))

  // Crawl results for two pages.
  .use(LinkedIn.crawl(pages))

  // Execute commands.
  .run(done);
```

## Writing the LinkedIn Nightmare plugin

The entirety of the code logic is [here](https://github.com/lambtron/nightmare-linkedin/blob/master/index.js).

## Creating an API

With Nightmare, you could even create an HTTP API. Set up a server with endpoints that accept requests, which contains information about a query, let's say an email address, then uses the plugin to search LinkedIn with the email, and ultimately returns to the API user the results.

Of course if LinkedIn notices suspicious activity from your server's IP, it is very likely that they will block any requests from it. In which case it is helpful to limit your use, proxy through other IP addresses, or just hire people from mechanical turk or other crowd service providers to retrieve data from/interface with LinkedIn for you.