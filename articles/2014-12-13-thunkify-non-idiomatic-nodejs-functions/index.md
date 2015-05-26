
# Thunkify Non-idiomatic NodeJS Functions

Yes, thunks are great. But what if the Object or Function that you're trying to thunkify doesn't fulfill the idiomatic node.js callback expression `function(err, res)`?

```javascript
var thunkedFunction = function() {
  return function(fn) {
    unthunkedFunction(function(err, param1, param2, param3) {
      if (err) fn(err, null);
      fn(null, { param1: param1, param2: param2, param3: param3 });
    });
  };
};
```

Now you can use it as such:

```javascript
var res = yield thunkedFunction();
```

Here is an example with [`superagent`](http://visionmedia.github.io/superagent/):

```javascript
var request = require('superagent');

/**
 * Thunkified POST.
 */

function post(uri, body) {
  return function(fn) {
    request
      .post(uri)
      .send(body)
      .end(fn);
  };
};

/**
 * Thunkified GET.
 */

function get(uri) {
  return function(fn) {
    request
      .get(uri)
      .end(fn);
  };
};
```

One other neat thing about thunked functions that if you need to test the expression to see if you are receiving an error, you can access that as such:

```javascript
var res = yield get("https://www.google.com");

get("https://www.google.com")(function(err, res) {
  // `res` here is the same as the `res` above
  // you can also access `err` here
});
```

Enjoy!