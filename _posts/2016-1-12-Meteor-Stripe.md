---
layout: post
title: 'Meteor Stripe Checkout'
date: '2016-1-12 1:48'
categories: programming
---

This tutorial will cover how to implement Stripe Checkout in your Meteor app.


{% highlight html %}

# Install Meteor globally
    curl https://install.meteor.com/ | sh

    # Create our Meteor app
    meteor create stripe-checkout

    # Change directories and start our new app
    cd stripe-checkout
    meteor



{% endhighlight %}


Now go to http://localhost:3000/ to view the default “Welcome to Meteor” homepage.


Now we're going to update your original scaffolded code, so we can start implementing your stripe integration.




{% highlight html  %}
# stripe-checkout.html
   <head>
     <title>meteor-stripe-checkout</title>
   </head>

   <body>
     <h1>Meteor Stripe checkout</h1>


   </body>

   <template name="hello">
     <button>Pay with Stripe</button>
   </template>

{% endhighlight %}

If you go back to http://localhost:3000/ you’ll see that the page has automatically updated with the new text!

Stripe setup and overview

Go over to Stripe and sign up for a free account. Then under “Your account” in the upper right corner, click on “Account Settings” and then API Keys.

Stripe provides you with 4 API Keys: 2 to use while in Test mode.

The “Test Publishable Key” is stored on the client and therefore public. The “Test Publishable Key” is stored on the server and should always be kept private.

We use the Publishable Key to generate a unique token from Stripe for each transaction. This token is then passed to our server where it is validated by the Secret Key. Stripe then processes the transaction.

To add Stripe to our Meteor app we will use the mrgalaxy:stripe package.

{% highlight html %}


  meteor add mrgalaxy:stripe

{% endhighlight %}




  Update your Javascript logic:


{% highlight js %}

# stripe-checkout.js
  if (Meteor.isClient) {
    Template.hello.events({
      'click button': function(e) {
        e.preventDefault();

        StripeCheckout.open({
          key: 'TEST-PUBLISHABLE-KEY',
          amount: 5000, // this is equivalent to $50
          name: 'Meteor Tutorial',
          description: 'On how to use Stripe ($50.00)',
          panelLabel: 'Pay Now',
          token: function(res) {
            stripeToken = res.id;
            console.info(res);
            Meteor.call('chargeCard', stripeToken);
          }
        });
      }
    });
  }

  if (Meteor.isServer) {
    Meteor.methods({
      'chargeCard': function(stripeToken) {
        check(stripeToken, String);
        var Stripe = StripeAPI('TEST-SECRET-KEY');

        Stripe.charges.create({
          source: stripeToken,
          amount: 5000, // this is equivalent to $50
          currency: 'usd'
        }, function(err, charge) {
          console.log(err, charge);
        });
      }
    });
  }

  {% endhighlight %}


Replace “TEST-PUBLISHABLE-KEY” and “TEST-SECRET-KEY” with your Stripe Test API Keys.


Update your html to use our stripe checkout button:

<a>https://gist.github.com/YonasBerhe/67bea4410e6ce7f1aa57</a>



Their you go, now you know how to use stripe checkout in your next meteor application.
