---
layout: post
title: How we build our Javascript
tags: !!null
---
This post isn't strictly about responsive design but hopefully it'll give you
some insight into how you can optimise your JS to help support the goals of a
responsive site.

<!-- more -->

## Perception of page speed

Speed is really really [important](http://www.useit.com/alertbox/response-
times.html). There's a ton of [research](http://blog.kissmetrics.com/loading-
time/) suggesting that users will simply leave a site and go elsewhere if they
perceive a site to be slow. We're currently developing the new [BBC News
site](http://m.bbc.co.uk/news) which has a large audience across the world
with many users connecting via GPRS who would not appreciate a large 1MB
monster.

Therefore many of the design choices we make focus on optimisation, both in
terms of the the size of the files and keeping the number of HTTP requests
down to a minimum.

## Cost of change

Over the last couple of years developers at the BBC have either been crying or
celebrating the loss of their beloved BBC [Glow](http://www.bbc.co.uk/glow/)
JavaScript library. The library itself was awesome but for the BBC it was
expensive to maintain. The consequence of announcing it's demise and the
adoption of jQuery meant sites had to upgrade. The migration effort to jQuery
was pretty big.

I say, 'was pretty big', but it's not really complete.

If you look at the source of many BBC sites built over the last couple of
years (Eg, [News](http://www.bbc.co.uk/news),
[iPlayer](http://www.bbc.co.uk/iplayer) …) you'll see many references to Glow
v1. Even recent projects (Eg, [Sport](http://www.bbc.co.uk/sport) - relaunch
in February 2012) seem resigned to using Glow in parts of their application
rather than migrating to something supported, such is the tight coupling of
some functionality to that library and expense of moving away from it.

And what is the reward for migration? To have to do it all again in 18 months
time when the project outgrows whatever library you pick or the library falls
out of favour, like last year's wetlook leggings.

To mitigate the effort needed in porting between libraries on a large site we
took a look around for better solutions to the problem. We wanted to make sure
our code was designed in a way that made swapping underlying code easier and
cheaper.

Addy describes the problem well in his [Scalable JavaScript
Patterns](http://addyosmani.com/scalablejs) talk and Stuart's presentation,
[Beyond Frameworks](http://blog.stuartherbert.com/php/2011/03/20/following-up-
my-beyond-frameworks-talk/) is also worth reading, albeit about PHP. Lastly,
and fortunately for us in the context of this essay, browsers have come a long
way in the last couple of years, with multiple vendors competing to support
HTML5 and other new APIs. This has given us an
[opportunity](http://blog.responsivenews.co.uk/blog/2012/03/08/cutting-the-
mustard/) to make the most of native functionality and to remove the chains of
the framework.

## Micro libraries

I'm the kind of guy that would always go for the pick 'n mix sweets over a
pre-packaged bag of goodies. Especially as I hate the liquorish ones.

jQuery is great but it ships with loads of stuff we don't need (35Kb worth).
We should only be sending the browser what code will actually be needed, and
slowly backing in to the frameworks where we feel we need the support.
Anything beyond that is wasteful and slows the experience of the site (think
GPRS in the middle of nowhere).

The idea with micro libraries is that you can cherry pick small self contained
modules of code to do a specific job which helps to avoid large monolithic
dependencies.

[Thomas Fuchs](http://twitter.com/thomasfuchs) has put together an excellent
[resource](http://www.microjs.com) of javascript micro libraries and its a
great place to explore and compare libraries for particular tasks.

What we use:

  * [Reqwest](https://github.com/ded/reqwest) - AJAX requests
  * [Bonzo](https://github.com/ded/bonzo) - DOM utility methods
  * [Qwery](https://github.com/ded/qwery) (mobile) - Selector wrapper around native functions
  * [Event Emitter](https://github.com/Wolfy87/EventEmitter) - PubSub
  * [Curl](https://github.com/cujojs/curl) - AMD Javascript loader

N.b. Dojo have started to go down this route, hopefully jQuery will go modular
too at some point.

## Loading what you need

Aside from carefully picking and choosing our framework dependencies wouldn't
it be awesome if we could manage them like we do with with rubygems or npms?
We can thanks to AMD.

The [Asynchronous Module Definition (AMD)](https://github.com/amdjs/amdjs-
api/wiki/AMD) spec allows us to inject specific libraries into the a chunk of
code and have them downloaded to the browser only when we need them.

This makes our pages load faster as we're not waiting for the scripts to load
at the start, instead they are deferred until we need them.

AMD provides two global methods, _define_ and _require_. The _define_ method
is used to create modules that will be used from other areas of the code:-


```js
        define(
            [modules/news, vendor/reqwest, vendor/pubsub],
            function(news, reqwest, pubsub){
                // Code goes here
            }
        );
```

So, in the example above the dependencies _['modules/news', 'vendor/reqwest',
'vendor/pubsub']_ are asynchronously loaded and references to them are mapped
to the factory function arguments, which provides a local sandboxed version of
them.

The _require_ method provides a sandboxed area where we can call in
dependencies.

AMD provides some big improvements to javascript including:-

  1. Groups code into manageable bits of functionality, that can be swapped in and out.
  2. Code is much easier to unit test.
  3. Code keeps out of the global scope, keeping it sandboxed on its own so it doesn't mess with other code on the page.
  4. Faster scope lookups as you only deal with local variables.
  5. Modules are far more portable to other projects as dependencies are injected and not scattered.
  6. Conditionally require library code, for example different code for particular browsers.

## Separation of business logic and DOM manipulation

My experience before of migrating projects from Glow to jQuery was that far
too much business logic was tangled up with complicated DOM manipulation and
when you needed to change the underlying library it was a total mess. Often it
was simpler to start again.

We don't use a full fledged MVC framework on the News project as we want to
keep things simple and lightweight but we still try and decouple code by
separating out 'things' into widgets, which each have a _logic_ part and a
_presentation_ part. This allows us to isolate the areas where DOM libraries
are being used and makes it far easier to understand where to go to change
code.

With the business logic module we write standard JavaScript that has no DOM
manipulation in there whatsoever. You can still use libraries (if you really
have to), but inject them using the AMD module pattern and use them sparingly.
I.e. don't wrap the structure of your code too deeply into the libraries API.

Below is a simple example widget using this approach for our module that loads
weather on to the front page :-


```js
        define([vendor/pubsub, vendor/ender/reqwest], function(pubsub, reqwest) {

            var Weather = function() {
                this.path = /weather;
                // subscribe to an external event
                pubsub.on(locator:locationChanged, this.updateWeather, this);
            };

            Weather.prototype = {
                updateWeather: function(location) {
                    var requestUrl = this.path + location.weather.id;
                    pubsub.emit(weather:requestStarted);
                    reqwest({
                        url: requestUrl,
                        success: function(response) {
                            pubsub.emit(weather:success, [response, location.weather.id]);
                        },
                        error: function(error) {
                            pubsub.emit(weather:requestError, [error]);
                        }
                    });
                }
            };

            return Weather;
        });
```

So, not much more than a constructor that listens for an external event (in
this case the user changing their location, Eg. from 'London' to
'Manchester'), which enacts some business logic (an XHR request) to fetch the
weather for the new location.

We've separated out the presentation of the weather to another module.


```js
        define([vendor/pubsub, vendor/ender/qwery], function(pubsub, $) {

            // Constructor function
            var WeatherView = function(selectorContainer) {

                // cache dom references
                this.container = $(selectorContainer);
                this.weatherLink = $(.weather-link, this.container);
                this.weatherLinkHref = this.weatherLink.attr(href);

                // set up event listeners
                news.pubsub.on(weather:success, this.handleSuccess, this);
            };

            // object methods
            WeatherView.prototype = {
                setContent: function(content) {
                    this.weatherLink.html(content);
                },
                setLink: function(locationId) {
                    this.weatherLink.attr(href, this.weatherLinkHref + forecast/ + locationId);
                },
                handleSuccess: function(response, locationId) {
                    this.setContent(response);
                    this.setLink(locationId);
                }
            };

            // Return the constructor
            return WeatherView;
        });
```

The presentation module listens for the 'weather:success' event and, upon
receiving that, rebuilds the UI.

## Inter-module communication

To decouple the modules and make them easier to test we use the mediator
pattern to communicate between them. The centralised pubsub system gives you a
lot of flexibility and reduces the fragility of depending on the existence of
foreign methods.

The idea is that your object fires custom events and other objects which have
subscribed to, will receive it. The publisher does not know about the other
objects or care whether the other object has received it or not and, likewise,
the subscriber does not know where the event came from. Think of it as fire
and forget.

PubSub is probably best explained with a diagram.

1. PubSub![](http://dl.dropbox.com/u/8856415/pubsub.png)

And the code looks something like this:

```js

        // Create a new EventEmitter instance (which we usually inject into each module)
        var pubsub = new EventEmitter();
        ...
        // The subscriber object registers for the event
        pubsub.on(menu:top:open, function() {
          that.sendStats(); // remember this is not bound to the outer object
        });
        ...
        // The publisher emits an event to the registry
        pubsub.emit(menu:top:open);

        // The Subscriber.sendStats() is then called by the central pubsub registry
```

## Next time

In the next part of this article we will go through how we test our
JavaScript.

