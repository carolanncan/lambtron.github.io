# Intermediate Cron Jobs with Heroku

We all know that Heroku has made app deployment as easy as git push heroku master (and, indirectly, educating the vast majority of junior junior devs a tiny sliver of git). Heroku even went ahead and built out an ecosystem of tools to help developers easily add services to their apps.

- Want to add a datastore? You can enable Redis to Mongo to Postgres.
- How about logging? Keen.io, Flydata, Logentries can help out.
- Monitoring? Again, you have Librato, Airbrake, New Relic, and Bugsnag, among others.
- Want to run schedule jobs? Well, there is Heroku Scheduler.

Yes, but if you've ever played with Heroku Scheduler, it unfortunately is very limited in flexibility, especially in the range and variety with which you can schedule jobs: only in 10 minute, 60 minute, or 24 hour intervals.

Heroku recently came out with clock processes for custom job scheduling. Here is some example code in nodejs to help demonstrate how it works.

In your Procfile, you define for Heroku the clock and the worker process, as such.

## Procfile:

```
web: node server.js
worker: node bot.js
clock: node clock.js
```

The clock.js file defines the singleton process (so as to avoid duplicate jobs queueing and complicated locking logic), whose only function is to schedule a task to the worker process. You can use any library to do so, but here we use npm's cron. The task that is executed is assigned to onTick.

## clock.js

```javascript
// clock.js
var CronJob = require('cron').CronJob;
var bot = require('./bot.js');

new CronJob({
  cronTime: "13 9,11,16,20,22 * * *", // everyday, 9:13, 11:13, 4:13, 8:13,
  onTick: bot.start(),
  start: true,
  timeZone: "America/Los_Angeles"
});
```

## bot.js

```javascript
// bot.js
module.exports = {
  start: function() { /* do stuff */ }
};
```

Hope this basic example helps people use Heroku's new custom clock processor to schedule jobs!