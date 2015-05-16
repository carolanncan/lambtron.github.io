
# How IFTTT, Mandrill, and Twilio is helping me find an apartment in SF

Anyone familiar with the SF apartment market understands the tremendous pain and frustrations in finding an available lease at a reasonable price. We have all heard and shared war stories of the hourly refreshing of Craigslist and bringing all of your bank statements and necessary paperwork to the open house just to win a chance to secure a lease for an apartment. In true SF-technology fashion, I decided to minimize response time to a new choice apartment listings on Craigslist by wiring together [IFTTT](https://ifttt.com/), [Mandrill](https://mandrillapp.com), and [Twilio](https://www.twilio.com) to auto respond with an open email and a click-to-call to my phone number and the number in the listing to schedule an appointment.

*Note 7/8/2013: Craigslist makes it impossible (please let me know if you were able to get around it) to allow Mechanize to retrieve HTML from Heroku's server. However, it works when I send a request from my local server. If anyone knows why it would make a difference, please leave a comment!*

## IFTTT
 
[IFTTT](https://ifttt.com/) ('If this, then that') allows you to create 'recipes' using the simple conditional structure 'If this, then that' and various ['ingredients'](https://ifttt.com/channels) (i.e. craigslist queries, instagram posts, buzzfeed articles, tweets). It is powerful due to its simplicity and flexibility; it can enable anyone to put the web to work without any programming knowledge.

I used IFTTT to send an email to my email address when there is a new Craigslist query that matches the me and my roommates' apartment listing preferences. This way, any new Craigslist listing that fits the query that I have set will be sent directly to my email inbox.

## Mandrill

I [setup a subdomain](http://help.mandrill.com/entries/21699367-Inbound-Email-Processing-Overview) (I added 'to' as the MX record and have it setup on my Mandrill account) responsible to receiving email messages and use Mandrill to parse the inbound email (similar to [how I built the A-List inbound parser](https://andymjiang.wordpress.com/2013/05/15/welcome-to-the-a-list/)). In the below code sample, it is important to note that you must first return a '200' to let Mandrill know that it is the right address to send the POST request of the inbound email.

```ruby
require 'mechanize'
 
class InboxController < ApplicationController
  include Mandrill::Rails::WebHookProcessor
  
  # For the apartment hunt.
  def parse_inbound_sf_apartment_email
    # Mandrill needs an ok in order to proceed to POST Mandrill events to this endpoint.
    if request.head?
      head :ok
    else
      # When receive new IFTTT email, use Mechanize to go to the URL of the listing.
      # Then get two things:
      # - anything that resembles a phone number
      # - a response email address
      # Afterwards, dial order for all roommates and connect w the broker
      # Then send a templatized email.
  
      if params['mandrill_events']
        text_body = ''
        JSON.parse(params['mandrill_events']).each do |raw_event|
          event = Mandrill::WebHook::EventDecorator[raw_event]
          text_body = event['msg']['text'].to_s
        end
  
        # Get the URL of the craigslist listing.
        url = text_body[/http\:(.*?)\.html/m]
  
        # Mechanize to get email address and phone number.
        a = Mechanize.new
        
        begin
          craigslist_listing = a.get(url.to_s)
        rescue ArgumentError
          # URL is not valid
          puts "\n\n\n\nURL IS NOT VALID\n\n\n\n"
          return "error"
        else
          # Regex to get email and phone number
          email_addresses = craigslist_listing.content.to_s.scan(/[\w.!#\$%+-]+@[\w-]+(?:\.[\w-]+)+/).uniq!
          phone_numbers = craigslist_listing.content.to_s.scan(/\W(\d{3}.?\d{3}.?\d{4})\W/m).uniq! - craigslist_listing.content.to_s.scan(/postingID=(.*?)\W/mi).uniq!
 
          # 'Click-to-call'.
          phone_numbers.each do |phone_number|
            # puts phone_number
            # Make outbound call to 314 (Andy, Jeff, whoever else).
            # Then, make outbound call to phone number.
            click_to_call(phone_number[0])
          end
 
          # Send templatized email to email_address.
          email_addresses.each do |email_address|
            Mailer.email_apartment_listing(email_address, 'Apartment listing').deliver
          end
        end
      end
      render 'app/views/inbox/sfapartments.html', :formats => [:html]
    end
  end
end
```

Once the email is successfully received and parsed, I use the [Mechanize library](http://mechanize.rubyforge.org/) to navigate to the Craigslist listing (line 31). Then, a few regex commands to find and scrape an email address (line 34) and a phone number (line 35). With the email address, I use ActionMailer (line 46) to send out a templatized email, cc'ing my roommates, asking to schedule an appointment to check out the apartment.

## Twilio

If I was successfully able to scrape a phone number from the Craigslist listing, then Twilio will automatically make two outbound phone calls (one to my mobile phone and one to the number on the listing). Throughout the day, I will be able to pick up my phone at my leisure and speak with somebody (if the call connects on the other end) to schedule an appointment.

Note that the routing must also be setup in your Rails app (the sample code is not shown here).

The first step is that we are sending a request to Twilio to initiate a phone call to my phone number (agent_number or '555-555-5555' in the example below) with dynamic TwiML (Twilio's XML-like proprietary markup language).

```ruby
require 'twilio-ruby'

class InboxController < ApplicationController
    BASE_URL = 'http://www.andyjiang.com/'
    
    # /click-to-call-request
    def click_to_call(calling_to)
        # debugging purposes.
        twilio_number = '4154444444'
        calling_to = digits_only(calling_to)

        agent_phone = '5555555555'
        caller_id = agent_phone

        # When we call the agent (andy + our apartment), we set the caller ID to the party we're eventually
        # calling. Why? So you could call them back from normal cellphone later.
        begin
            @client = Twilio::REST::Client.new ACCOUNT_SID, AUTH_TOKEN
            @client.account.calls.create(
                :from => twilio_number,
                :to => agent_phone,
                :url => BASE_URL + '/click-to-call-callscreen?calling_to=' + calling_to + '&caller_id=' + caller_id
            )
            puts "click to call initiated."
        rescue
            400
        end

        render 'app/views/inbox/sfapartments.html', :formats => [:html] 
    end
    
    # /click-to-call-callscreen
    # This handler receives an HTTP callback from Twilio once the agent has picked up
    # their phone. It first verifies that they're ready to receive the call (to avoid
    # voicemail) and then defers to the final twiml URL.
    def click_to_call_callscreen
        @post_to = BASE_URL + '/click-to-call-twiml?calling_to=' + params['calling_to'] + '&caller_id=' + params['caller_id']
        render :action => 'clicktocallcallscreen.xml.builder', :layout => false
    end

    # /click-to-call-twiml
    # This handler is fired if the callscreen detected a digit
    def click_to_call_twiml
        @calling_to = params['calling_to']
        @caller_id = params['caller_id']
        render :action => 'clicktocall.xml.builder', :layout => false
    end
    
    def digits_only(string)
        return string.gsub(/[^[0-9]]/, "")
    end
end
```

The first dynamic TwiML (line 38 above and 'clicktocallcallscreen.xml.builder' below) tells Twilio to listen, upon a call connecting with my phone (agent_number or '555-555-5555'), for a keypad response indicating that I want to be connected with the Craigslist lister's phone number. If I hit a key, then proceed to dial the other number and connect us. Note that I am  providing yet another dynamic action URL ('@post_to') that will populate the final TwiML with the correct phone numbers that will be connected in the final call.

```ruby
xml.instruct!
xml.Response do
    xml.Gather(:action => @post_to, :numDigits => 1, :timeout => '10') do
        xml.Say "Press a key to accept", :voice => :woman
    end
    xml.Say "You didn't press anything. Goodbye.", :voice => :woman
    xml.Hangup
end
```

It is in line 46 in inbox_controller.rb where the clicktocall.xml.builder (below) is called dynamically (with '@calling_to' and '@caller_id' both being passed to the TwiML).

```ruby
xml.instruct!
xml.Response do
    xml.Dial @calling_to, :callerId => @caller_id
end
```

The result is Twilio automatically connecting me with the phone number listed on the Craigslist post. This is a derivation of a very popular Twilio use 'Click-to-call' that many lead management and sales teams use to better engage with their customers.

## Conclusion

Now I don't have to be on Craigslist 24/7 to find a suitable apartment listing; I can just wait for my web server to send out emails or phone calls to schedule appointments with Craigslist posts that fit our preferences. Now if there were only a way to automate the showing up, preparing the paper work, competing against other bidders, and the remainder of the apartment leasing process.

