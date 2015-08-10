
# Reading Position Horizontal Progress Bar

I've seen this in [some of the articles](http://firstround.com/review/how-dropbox-sources-scales-and-ships-its-best-product-ideas/) on the Internet—a subtle, horizontal bar at the top of the window that fills up as you scroll down, indicating how far you've read. I came across [this solution](http://stackoverflow.com/questions/19700020/change-progress-bar-value-based-on-scrolling) that uses jQuery, but wanted to write one using vanilla JavaScript. Here is the code!

You can check out a live demo right on this site. Scroll down and check out the horizontal progress bar at the top of the window!

The HTML is a `div` with `width` at `100%` and a `span` inside with variable `width`. Both the `div` and `span` will be fixed at the top and will have heights of `2px`. Here is the HTML:

```html
<div id="Reading-progress">
  <span id="Progress-bar" class="bar"></span>
</div>
```

And the styling below. Note that the position is `fixed` at `top: 0px` and `left: 0px`.

```css
#Reading-progress {
  position: fixed;
  top: 0px;
  width: 100%;
  height: 2px;
  margin: 0px;
  left: 0px;
  z-index: 99;
}

.bar {
  height: 2px;
  background: #5CCC5C;
  position: fixed;
  top: 0px;
  z-index: 99;
}
```

The JavaScript attaches a `scroll` event to the `window`, calculates the scroll percentage, and updates the `width` of the `span` via `setAttribute()`. Note that using `setAttribute()` for `style` will replace all other `style`s on the element, which is why we separate the class `bar` from the id `Progress-bar`.

```javascript
window.addEventListener('scroll', function(e) {
  var s = (window.pageYOffset !== undefined) ? window.pageYOffset : (document.documentElement || document.body.parentNode || document.body).scrollTop;
  var body = document.body;
  var html = document.documentElement;
  var d = Math.max(body.scrollHeight, body.offsetHeight, html.clientHeight, html.scrollHeight, html.offsetHeight);
  var c = window.innerHeight;
  var position = (s / (d - c)) * 100;
  document.getElementById('Progress-bar').setAttribute('style', 'width: ' + position + '%');
});
```

Have fun!

*Like what you read? Share this article and follow me on [Twitter](http://www.twitter.com/andyjiang) for random musings.*


<div id="Reading-progress">
  <span id="Progress-bar" class="bar"></span>
</div>

<style>
#Reading-progress {
  position: fixed;
  top: 0px;
  width: 100%;
  height: 2px;
  margin: 0px;
  left: 0px;
  z-index: 99;
}

.bar {
  height: 2px;
  background: #5CCC5C;
  position: fixed;
  top: 0px;
  z-index: 99;
}
</style>

<script>
window.addEventListener('scroll', function(e) {
  var s = (window.pageYOffset !== undefined) ? window.pageYOffset : (document.documentElement || document.body.parentNode || document.body).scrollTop;
  var body = document.body;
  var html = document.documentElement;
  var d = Math.max(body.scrollHeight, body.offsetHeight, html.clientHeight, html.scrollHeight, html.offsetHeight);
  var c = window.innerHeight;
  var position = (s / (d - c)) * 100;
  document.getElementById('Progress-bar').setAttribute('style', 'width: ' + position + '%');
});
</script>