---
layout: post
title: Axes of responsive design
tags:
- DAP
- product design
- creative technologasm
---
The early responsive design [literature](http://www.abookapart.com/products
/responsive-web-design) talked about fluidity of the layout and embracing
uncertain and uneven properties of web browsers but the early
[pioneering](http://www.bostonglobe.com/) efforts have stopped at responsive
_web design_, in essence an interaction and CSS problem ignoring the other
aspects of the many and varied types of http clients.

<!-- more -->

I think over the coming months responsive design will evolve in to other
areas, responsive _bandwidth design_, responsive _cpu design_, responsive
_content design_, taking the mobile first approach to more than just screen
width and applying it to all the properties a web client might possess.

For our current project we are attempting to adapt and respond to the
following axes:-

  * Screensize - Orientation, resolution, viewport etc.
  * Connection speed - Bandwidth can be fast, slow, offline or change over the course of a session.
  * Standards support - CSS, JS, HTML5 etc.
  * Colour - From monochrome, poor screens in different light, to rich high-resolution graphics.
  * Interaction - Touch, mouse, voice, keyboards.
  * Performance - CPU, memory, battery life.

A lot of these properties we can detect already with simple [media
queries](http://www.w3.org/TR/css3-mediaqueries/) or plain old JS feature
detection although some we'll need to wait a little longer to sniff
accurately.

For example, there's no good way to detect CPU on the client but as the [DAP
project](http://www.w3.org/2009/dap/) moves from W3 meetings to specifications
and from nightlies to mainstream releases developers will have a set of
detailed hooks on which our interfaces can adapt.

Being able to dynamically respond to the users device in this way is a kind of
surreptitious, ambient personalisation, giving the user a optimised experience
without them having to visit a different URL.

In the future one can imagine decorating html documents with a handle to
control [performance intensive](http://dev.w3.org/2009/dap/system-info/)
aspects of pages like complex animations. Perhaps if the users battery is 10%
we shut down background http requests or [tone down the whole
UI](http://www.blackle.com/).

The interesting challenge will be to see how we make this usable. A UI that
adapts under your feet is an unpredictable one.

