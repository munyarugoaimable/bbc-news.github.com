---
layout: post
title: It's time for a brand new kitchen
tags: !!null 
---
A few weeks ago we [blogged about our browser
support](http://blog.responsivenews.co.uk/blog/2012/03/08/cutting-the-
mustard/) and how we bootstrap our JavaScript. The post was called "Cutting
the mustard" and it got me 5 new followers on twitter (awesome!). Matthew
Pennell responded with his blog post ["Throwing out the kitchen
sink"](http://www.thewatchmakerproject.com/post/throwing-out-the-kitchen-
sink/).

Matt said that our browser grading strategy is very aggressive, using
progressive enhancement to only support modern browsers with the intended
experience. Matt also said that this is unneccessary as JavaScript libraries
(such as jQuery) allow us to support all browsers, and that worrying about the
download speed was unneccessary. Here's my re-response. Matt, if I've
misquoted you out of context please tell me where, I'll do my best to change
it to the read good.

My re-response is split into:

  * Download size IS important
  * Our progressive enhancement strategy means MOST browsers receive JavaScript
  * But yes, IE8 is a big issue

<!-- more -->

My slightly inferior colleague Jane Cheeseley spoke about our use of
JavaScript libraries ([click
here](http://blog.responsivenews.co.uk/blog/2012/04/13/how-we-build-our-
javascript/)) which I think answers Matt's correct critique that JavaScript
libraries are a good thing.

To summarise the summary, the point I'd make is that speed is a feature, and
that users just want the content. Our web browser support is definitely skewed
towards the type of content that BBC News provides, so it might not be
relevant to everyone, but its definitely the right thing to do.

## Baby with the bath water

I said this in the original post and I'll restate it now: broadband made us
all lazy. Matt said "the golden rule I grew up with – no page should ever be
over 100Kb – has long since been mothballed", I disagree with Matt, I think
this golden rule has become very relevant again. One assertion we've made in
our team is that there is no mobile web.

The idea of a "mobile web" means that there is somehow two different
internets. Right now I'm writing this sentence on a laptop that is tethered to
my phone for mobile broadband, tonight I'll be reading twitter using the same
phone but connected to the wifi at my house. Which one is the mobile web?

Screen size != bandwidth capacity.

But don't be worried, because this is an excellent opportunity for us as a
discipline to retake control of how web pages are made. The two most important
attributes of a page to a user is:

  * Can I view the data right now
  * Is the data relevant to me

And seeing as there is no such thing as the mobile web it means we need to
make sure these two needs are meet for everyone. Matt talks about browser
guidelines, and having a lowest common denominator with a list of A graded
browsers. I think this also has to take into consideration another lowest
common denominator: connection type.

GPRS is the slowest connection speed we get in the UK, there is probably a
slower one somewhere in the world, if so please comment below. GPRS is roughly
equivalent to old dialup speeds, and so if you want a page to load within 10
seconds you need to get the total file size of your page to be about 60kb.

[m.guardian.co.uk](http://m.guardian.co.uk) is 50kb if you don't include the
adverts. jQuery is 70kb-ish uncompressed and 35kb compressed. Even the jQuery
Core dev team[ think its too big](http://blog.jquery.com/2011/11/08/building-a
-slimmer-jquery/) to be used on the "mobile web".

If you really believe that the 100kb rule should be mothballed, then what
limit would you set a download size to be and why?

## We are the 99%

In his post Matt also says "A single line of JavaScript leverages browser
feature detection to decide whether you’re one of the chosen few:", this is
the part I disagree with most. You won't be "one of the chosen few" as the
vast majority of desktop browsers and a majority of mobile browsers pass this
test.

To recap, in the original post I split browsers into two camps: HTML4 and
HTML5. The HTML4 camp does not pass the test, and so gets a straight forward,
non JavaScript experience. This experience is very fast.

The HTML5 camp does pass the test, so they download additional CSS (with fancy
CSS selectors and modern CSS properties), an additional JavaScript file and
extra content.

The biggest difference in the two experiences is the extra content, for
example the "most popular" tab is loaded in via AJAX for HTML5 browsers, where
as a HTML4 browser would click the tab and get a new page load into the
browser.

All the core content is correctly still available to all browsers. It's a
progressive enhancement that still allows 100% of our users to view the
content. The difference is in how they see that content. I'm not sure how else
we can do this with responsive web design and still keep the file sizes at a
reasonable size.

## IE8, a browser too far

Matt rightly points out that IE8, sitting in the HTML4 camp and with a highly
significant percentage of the traffic should not get the limited, HTML4
experience as it is easy to accomodate for using a JavaScript library.

This is where the mustard starts to get a bit too spicy. IE8 was almost a
really great browser, however it isn't. We're biding our time with it.
Sometime in the not so distant future the BBC News site will be completely
responsive. However right now we do have two different products (www and m).

As a developer I'd like to pursue the purist approach and keep IE8 a HTML4
browser, however there will more than likely be pressure from management to
give it more love because it will probably still have a significant user base.

When that time comes we will need to make a decision about IE8, we will either
keep it as a HTML4 browser or polyfill it using a conditional comment to
include a HTML5 upgrade to make the browser cut the mustard. This is a special
case though due to the unique way IE collects users and doesn't automatically
upgrade them. It's also the one compromise we feel we will probably need to
make. Web development is full of compromises.

