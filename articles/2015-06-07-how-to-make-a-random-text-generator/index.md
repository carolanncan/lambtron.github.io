
# How to Make a Random Text Snippet Generator in atext

If you've ever written into support at Segment, you've probably received an email from one of us with a whimsical "inspirational" quote appended to the end. But how do we keep the footer quotes different each time? We use a special script with [atext](https://www.trankynam.com/atext/) in order to keep the quotes random and fresh—here is how we do it!

> "If you can dream it, you can query it."  

>    *Walt Disney*

..and the response from one of our lovely customers:

![](http://i.imgur.com/WyDSUES.png)

Just copy and paste the below script into your atext!

```
#!/bin/sh
 
# Keep this updated when you add or take away quotes on the case list
num_quotes=15
 
# Generate a random quote number variable, 'rand'
rand=$[ ( $RANDOM % $num_quotes ) + 1 ]
case $rand in  #BEGIN CASE
        1) quote="\"Our greatest weakness lies in giving up in identifying key performance metrics. The most certain way to succeed is always to try one more time.\"
    Thomas Edison";;
        2) quote="\"If you can dream it, you can query it.\"
    Walt Disney";;
        3) quote="\"You are never too old to set another goal, to dream a new dream, or to track your business growth with analytics.\"
    C.S. Lewis";;
        4) quote="\"Keep your eyes on the stars, your feet on the ground, and your customer acquisition cost lower than its lifetime value.\"
    Theodore Roosevelt";;
        5) quote="\"Great minds discuss business insights; average minds discuss KPIs; small minds discuss metrics.\"
    Eleanor Roosevelt";;
        6) quote="\"Good artists copy, great artists steal, best artists gain actionable insights from their data.\"
    Pablo Picasso";;
        7) quote="\"Success is walking from failure to failure with no loss of enthusiasm or data integrity.\"
    Winston Churchill";;
        8) quote="\"I have not failed. I've just found 10,000 KPIs that are meaningless.\"
    Thomas Edison";;
        9) quote="\"The starting point of all achievement is desire and adding funnel analytics to your website.\"
    Napoleon";;
        10) quote="\"I find that the harder I work and the better I optimize my conversion funnels, the more luck I seem to have.\"
    Thomas Jefferson";;
        11) quote="\"The successful warrior is the average man, with laser-like focus and empowering business intelligence reporting tools.\"
    Bruce Lee";;
        12) quote="\"You must expect great insights in your customer data before you can query them.\"
    Michael Jordan";;
        13) quote="\"You miss 100% of the your business and growth insights that you don't query.\"
    Wayne Gretzky";;
    14) quote="\"Only you can prevent data loss.\"
    Smokey the Bear";;
    15) quote="\"Strength and conversion funnels are all that I am.\"
    Gandhi";;
esac  # END CASE
 
# Display the random quote from case statement, and format it to line wrap at 80 characters
echo "Andy Jiang\n\nSegment | Growth Evangelist

$quote"
```

Enjoy!

*Like what you read? Share this article and follow me on [Twitter](http://www.twitter.com/andyjiang) for random musings.*