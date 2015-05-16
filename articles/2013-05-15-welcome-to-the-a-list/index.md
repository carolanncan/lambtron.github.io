
# Welcome to the A-List

Recently, a conversation over dinner about personal life hacks without the direct intention of blatant commercialism spawned a neat idea that scratches an itch that us west coast jet setters face occasionally—not only just checking into Southwest flights, but also checking into those flights as early as possible to attain a highly coveted 'A-list' boarding group.

There have been too many times when the 24 hour period prior to the flight occurs when I'm away from my computer. This [web application](http://www.andyjiang.com/sw), however, will check into your Southwest flight for you.

Instead of going to a [website](http://checkintomyflight.com/) where you input the information necessary for you to check in (name and confirmation number), you now can you just forward your Southwest flight confirmation email (the one with the subject that says 'Your trip is right around the corner!') to sw@to.andyjiang.com. Then, when the 24 hour check in window opens up, the web application will check in to the flight for you.

*Note (7/01/2013): Southwest asked me to take down the app! Happy hacking everyone!*

*Note (5/31/2013): Recently, this app was featured in [Lifehacker](http://lifehacker.com/southwest-a-list-automatically-checks-you-into-southwes-510333096). Kindly note that this app is still in beta and there are probably many bugs that have yet to surface themselves (one such obvious one is checking in multiple passengers, as I wrote this for me and my buddies and didn't have children to buy tickets and for whom to check-in). If you do run into any issues or have any feedback, feel free to tweet at me @andyjiang or comment on this blog post and I will make a note of it.*

## How does the app work?

The Mandrill (hosted by Mailchimp) server receives an inbound email and sends a POST request to the web server. The web server then extracts the text from the body of the email from the POST request and runs several regular expressions on it to get the following pieces of information: first name, last name, confirmation number, your email address, the URL it needs to go to in order to check in for you. It saves this data into a Mongo database (MongoHQ add-on with Heroku).

An hourly task runs on the server (Heroku Scheduler) to look at all of the records in the mongo database; for each record, if it is time to check in, the web server  uses the Mechanize library to get and parse the HTML of the confirmation web site, fill in the required information, and check in for you. Then it takes your boarding group and boarding position and sends you a confirmation email.

## Tools I used

Since debugging is 90% of any project, it is important to learn to break the project into small, achievable goals, to quickly isolate problems and to iterate through working solutions. Below is a list of some of the tools I used to test quickly and build out the application.

[**Rubular**](http://rubular.com/): Regular expressions were critical to parsing the body of the inbound email. This nifty website allows you to test all of your regexp patterns against Strings of your choice. Combined with [IRB](http://en.wikipedia.org/wiki/Interactive_Ruby_Shell) and [`.class?`](http://ruby-doc.org/core-2.0/Class.html), you can very quickly determine the right commands to extract the information needed from the text.

[**Localtunnel**](http://progrium.com/localtunnel/): Localtunnel, hosted by Twilio, allows you to expose an endpoint on your local server to receive HTTP requests from other servers. This is important when you are using a service that will send you [webhooks](http://en.wikipedia.org/wiki/Webhook) (Twilio, Mandrill inbound emails, etc.), so you don't have to push your changes to a production server in order to test whether your app correctly processes the POST request to your endpoint. With Localtunnel, you just follow the instructions on the [site](http://progrium.com/localtunnel/), copy and paste the localtunnel URL/endpoint generated (tied to your local server), and provide it to the web service so it knows the address to send its request, thereby allowing you to test locally.

**View Elements in Developer Tools**: In Chrome (Firefox and Safari, also), you can view the HTML document of each page you are on by going to View > Developer > Developer Tools (or Option + Command + I). This is important when you are using [Mechanize](http://mechanize.rubyforge.org/), which allows you to parse HTML given the HTML element, id, class, name, etc. I would test code by using [IRB](http://en.wikipedia.org/wiki/Interactive_Ruby_Shell), initialize a Mechanize object, open up the browser, and navigate the web from the terminal.

## Conclusion

Feedback is always welcome. If you think you are going to forget to use this next time you fly Southwest, go into your Gmail account right now and set up an instant email forwarding. Go to your Gmail's Settings > Forwarding and POP/IMAP > Forwarding, then select Add a Forwarding Address. Type in "sw@to.andyjiang.com" and send the verification email that will contain the confirmation code.

You will receive shortly an email that will provide you with the confirmation code that you can enter in the above field. The return email will also include a link that you can just click on to verify and confirm sw@to.andyjiang.com as the new forwarded email address.

Once you confirmed the email address to allow you to auto forward to it, then you will have to set up the filter for the right emails to be auto forwarded. Click on the link in the tip 'You can also forward only some of your mail by creating a filter!' and then either copy and paste "from:southwest subject:(Your trip is around the corner)" into your Gmail search bar or use the template in your 'Show Search Options' drop down below.

That should set it up so that any future email from Southwest with that particular subject will be automatically forwarded to my web app, which should hopefully check you in so you won't have to sit in the back between the two fat, sweaty guys.

I will blog more about the actual code in future posts.

If you have any questions, please let me know!