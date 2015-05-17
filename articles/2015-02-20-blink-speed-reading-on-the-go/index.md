
# Blink: Speed Reading on the Go

We've all heard about [Squirt](https://www.squirt.io/)—a tool that allows you to speed read with a technique that minimizes eye scanning, thereby effectively helping you reach WPMs of 1000 (allegedly). [Blink](http://blinkread.herokuapp.com) is the mobile version of Squirt.

## How to use

On your mobile device, first copy any URL so that it is on your clipboard. Then open up [Blink](http://blinkread.herokuapp.com) on your device (if you're on iOS, I recommend saving it to home screen!). Lastly, paste the URL in the app and wait. You will soon see this:

![](http://i.imgur.com/JRZJKDv.gif)

## How does it work

I created a [separate API service](https://github.com/lambtron/fetchtext-api) that would take any URL and return just the text. This API service uses [request](https://github.com/request/request) to get the HTML of the URL and then [Unfluff](https://github.com/ageitgey/node-unfluff) to extract the main text from the HTML.

[Demo](http://fetchtext-api.herokuapp.com/fetch/?url=http://www.polygon.com/2014/6/26/5842180/shovel-knight-review-pc-3ds-wii-u).

Then, on the client side of this app, it tokenizes the text body into an array of words, find the 'pivot' letter in each word, then display it as such one by one. Also if we see a period or other punctuation, we will insert additional punctuation in the array to give it the illusion that we are pausing.

The pivot function:

```javascript
this._getPivot = function(word) {
  var i = 5;
  switch (word.length) {
    case 1:
      i = 1; // first
      break;
    case 2:
    case 3:
    case 4:
    case 5:
      i = 2; // second
      break;
    case 6:
    case 7:
    case 8:
    case 9:
      i = 3; // third
      break;
    case 10:
    case 11:
    case 12:
    case 13:
      i = 4; // fourth
      break;
    default:
      i = 5; // fifth
  };
  return i;
};
```

Actually showing the word on the screen. The parameter `i` represents the index in the array `this.words`.

```javascript
this._show = function(i) {
  var p = this._getPivot(this.words[i]);
  var html = '<span class="left"><span class="pre-pivot">' + this.words[i].substr(0, p - 1) + '</span></span>';
  html += '<span class="right"><span class="pivot">' + this.words[i].substr(p - 1, 1) + '</span>';
  html += '<span class="post-pivot">' + this.words[i].substr(p, this.words[i].length - p) + '</span></span>';
  (this.el).innerHTML = html;
};
```

The styling so that the pivot character is always in the middle:

```css
span.left {
  display: inline-block;
  position: absolute;
  left: 0px;
  height: 100%;
  width: 650px;
}

span.right {
  display: inline-block;
  height: 100%;
  margin-left: 565px;
  width: 300px;
}

span.pivot {
  color: red;
}

span.pre-pivot {
  float: right;
}
```

[Demo](http://blinkread.herokuapp.com). [Source](https://github.com/lambtron/blink).