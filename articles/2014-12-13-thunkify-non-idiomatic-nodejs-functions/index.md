
# Thunkify Non-idiomatic NodeJS Functions

Yes, thunks are great. But what if the Object or Function that you're trying to thunkify doesn't fulfill the idiomatic node.js callback expression `function(err, res)`?

```
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

```
var res = yield thunkedFunction();
```

Enjoy!