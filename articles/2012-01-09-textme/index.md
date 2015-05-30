# TextMe: A Bookmarklet That Lets You Send Highlighted Text In Your Browser To Your Phone

TextMe! is straightforward to use: type in your phone number in the field and generate a link that is then dragged to your toolbar. Whenever you want to text yourself something, just highlight it in the browser and click the bookmarklet.

## TextMe!

*NOTE: Since I am no longer employed at Twilio, my Twilio account is no longer funded and therefore this bookmarklet no longer is operating. However, you can build your own and use your own Twilio account!*

A few weeks ago, I built this derpy little bookmarklet that lets you send highlighted text in your browser to your phone as an SMS. My friend Jen actually inspired this app and I realized how useful it would be when I'm on my way out and don't want to retype the address of the restaurant into my phone. I also felt that this would be a good project to improve my javascript (I've never written a bookmarklet), as well as learn to work around Cross-Origin Resource Sharing.

The technology stack for this project is HTML/css/javascript/jQuery, Rails, Ruby, Heroku.

## Things I've Learned

### Bookmarklet Javascript

The bookmarklet, essentially, is a function that is executed whenever you click on it. In this case, the function did the following things: attach a script to the HTML document body, execute that appended script, import two functions ("getSelText" that will capture the highlighted text and "sendSMS" that will send a POST request to my server, thus sending a request to Twilio to deliver the SMS), and then calling "sendSMS" that will initiate the SMS delivery.

Here is the code for the TextMe! landing page. This essentially shows how I took the user's phone number, generated a script (with the particular phone number) that will make the sendSMS call to the server, and put that into an anchor tag that the user can drag to the toolbar. Line 17 is the javascript function that ends up in the bookmarklet that is dragged to the toolbar:

```
<section class="form">
    <input type="text" placeholder="xxx-xxx-xxxx">
    <button type="submit">Make my bookmarklet!</button>
</section>

<section id="bookmarklet-container">
    <span><p>Your link will be generated below and drag it to your toolbar!</p></span>
    <div id="bookmarklet">
    </div>
</section>

<script type="text/javascript">
    $(document).ready( function() {
        $('.form > .btn').click( function() {
            var recipient = $('.form .input-medium').val();
            if ( recipient != "" ) {
                var href = "javascript:(function(){var s=document.createElement('script');s.src='http://www.andy-jiang.com/text2.js';s.onload=function(){sendSMS(" + recipient + ")};document.body.appendChild(s);})();";
                // Make the draggable bookmarklet.
                var a = document.createElement('a');
                a.title = "TextMe!";
                a.innerHTML = "TextMe!";
                a.href = href;
                $('#bookmarklet').html(a);
            }
        });
    });
</script>
```

When you click on the bookmarklet after it is dragged on your toolbar, the function appends some javascript to the HTML document and imports the below two functions. Then, once everything is imported successfully, sendSMS is called with the phone number passed as an argument. The function sendSMS will get the selected text and send a request to the server (passing the recipient's phone number and the body of the SMS, which is the highlighted text) to then ultimately send the SMS.

```
function getSelText() {
  var txt = '';
  if (window.getSelection) {
    txt = window.getSelection();
  } else if (document.getSelection) {
    txt = document.getSelection();
  } else if (document.selection) {
    txt = document.selection.createRange().text;
  } else return;
  return txt;
}

function sendSMS( recipient ) {
  var body = getSelText();

  if ( body == '' ) {
    alert("You have not highlighted anything to send to your phone!");
  } else {
    // Create object.
    var http = new XMLHttpRequest();

    // POST
    var data = "recipient=" + recipient + "&body=" + body;
    var url = "http://www.andy-jiang.com/textme/send_sms/";
    http.open("POST", url, true);
    http.setRequestHeader("Content-type", "application/x-www-form-urlencoded");
    http.send(data);
    alert("A text was just sent to " + recipient);
  }
}
```

### Javascript without jQuery

Because this is a bookmarklet, the intention was to keep it as lightweight with minimal dependencies. As such, it made sense (and was a great challenge) to write all of the bookmarklet javascript without jQuery to help append scripts and traverse the document object model. Note the substantial amount of jQuery used in the first code sample.

### Cross-Origin Resource Sharing (CORS)

In computing, an important security concept for browser-side programming was what is known as 'same origin' policy. This policy allows scripts running on the same 'origin' (domain, hostname, web server) to access each other's methods, properties, and attributes with no specific restrictions. However, this policy prevents scripts on one domain to access the methods, properties, and attributes of another domain (imagine if you could write some javascript on my hostname that can call methods on another web server—there are obvious security implications and risks involved).

In some instances, it is necessary to pass data from the client side of one hostname to the server of another domain, as is in the case of this bookmarklet. TextMe! attaches and runs a script on any website on the Internet, which takes the highlighted text and POSTs that data to my humble little WEBricks Heroku-hosted web server. In these cases, CORS is one modern way of allowing for these interactions to happen.

CORS defines a specific way to allow for cross-domain requests to occur. The browser making the cross-domain request to the server has to pass an 'Origin HTTP header', indicating its domain. Then, the web server responds with an 'Access-Control-Allow-Origin' header with a value denoting which origin sites are allowed.

Please see the example from my server side Ruby code below. Line 33 an onward shows the header response with 'Access-Control-Allow-Origin: *', meaning to allow every cross-domain request.

```
require 'rubygems'
require 'twilio-ruby'
require 'net/http'
require 'uri'
require 'json'

class TwilioController < ApplicationController
  TWILIO_ACCOUNT_SID = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'
  TWILIO_ACCOUNT_TOKEN = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'
  SENDER_NUMBER = '+xxxxxxxxxx'

  def send_sms
    set_access_control_headers
    head :ok

    # Get recipient phone number from POST.
    number = params["recipient"]

    # Get body from POST.
    body = params["body"]

    # Send sms.
    twilio_client = Twilio::REST::Client.new TWILIO_ACCOUNT_SID, TWILIO_ACCOUNT_TOKEN
    twilio_client.account.sms.messages.create(
      :from => "#{SENDER_NUMBER}",
      :to   => "#{number}",
      :body => "#{body}"
    )
    render :file => 'app/views/twiliocon/index.html'
  end

  private
  def set_access_control_headers
    headers['Access-Control-Allow-Origin'] = '*'
    headers['Access-Control-Allow-Method'] = 'POST, GET, OPTIONS'
    headers['Access-Control-Max-Age'] = '1278000'
    render :json => { :success => true }
    end
end
```

Note that I didn't include any phone number validation, which should probably be included to some extent.

## Conclusion

For a quick project, TextMe! certainly challenged me in new areas and helped me learn as a developer. Understanding how bookmarklets worked, writing jQuery-free javascript, and getting a better grasp of Cross-Origin Resource Sharing proved to be achievable, yet challenging goals.

Features to add in next version:
- easier to drag the bookmarklet to the toolbar
- form validation for the phone number!

So try it out and let me know what you think!

*Thanks to Jen for the original idea, Yuning for proofreading a draft of this in 30 seconds, as well as Mike and Song who helped out with various engineering aspects of the project.*

*Like what you read? Share this article and follow me on [Twitter](http://www.twitter.com/andyjiang) for random musings.*