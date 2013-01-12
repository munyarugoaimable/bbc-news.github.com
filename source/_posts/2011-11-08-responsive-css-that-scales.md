---
layout: post
title: Responsive CSS that scales
tags:
- CSS
- LESS
- OOCSS
---
CSS for a big site is different to CSS for a small, or even medium sized-site.
The way CSS was intended, when you create a new piece of functionality for
your website you simply write some new CSS to style it - easy.

<!-- more -->

```css

        #feature-4 {
            width: 50%;
            float: left;
            clear: both;
        }

        #feature-4 article:first-child h2 {
            color: red;
        }





    # Feature 4







    ## Something happened!


```




![](http://static.responsivenews.co.uk/examples/img/less-css-vis1.png)

This works well for a small website as because it gives you direct control
over every element. For a medium-sized site you can scale your CSS by applying
a few HTML patterns to your features, then simply writing your CSS to style
those patterns rather than the features individually.

```css

        .feature-4 {
            width: 50%;
            float: left;
            clear: both;
        }

        .latest-item h2 {
            color: red;
        }





    # Feature 4







    ## Something happened!



```



Then you try to write CSS for a big website. A website with hundreds of
features, hundreds of thousands of pages. A site where you need to ensure
features remain consistant when they are moved to places you never expected
them to go, whilst providing a robust canvas for external developers to drop
untested code on the page expecting it to 'just work'. Whilst many languages
let you manage your code by scoping it to particular features, the best option
CSS [currently](http://www.w3.org/TR/css3-namespace/) offers is the ability to
use classnames or IDs to namespace areas of your HTML. This works for
individual features, but if you attempt to use namespacing higher up the HTML
document tree to share code amongst multiple features you can easily end up
with an unpredictable
[cascade](http://www.w3.org/TR/CSS2/cascade.html#cascade) of conflicting style
rules that are only possible to control by writing _further_ styles with
increased selector [specificity](http://diythemes.com/thesis/css-specificity-
thesis/).

1. Messy CSS optimisation![](http://static.responsivenews.co.uk/examples/img
/less-css-figure1.png)

Optimising CSS for re-use in the present almost always ends up limiting your
ability to reuse that CSS in unexpected ways in the future. This leads to the
situation apparent on many large websites where developers pragmatically bolt
layer-upon-layer of high-specifity CSS overrides on top of prematurely
optimised global styles. Whilst this works, it causes such a complex
environment of style inherentance that designs very quickly become unwieldy to
change.

Look at fig.1. If you had a bug in HTML feature #4, which CSS file would you
look at first? Once you track down & fix the bug, how do you predict if any
other HTML features might have been using the style you just changed? Should
they all be retested to make sure they haven't been affected by the change?
How about branching a feature for some multivariate testing, or retiring one
completely?

## Object Oriented CSS

2. OO-CSS structure![](http://static.responsivenews.co.uk/examples/img/less-
css-figure2.png)

Recognising this problem, Nicole Sullivan introduced the idea of [Object
Oriented CSS](http://www.stubbornella.org/content/2009/02/28/object-oriented-
css-grids-on-github/) a couple of years ago. I'm not going to discuss the
technique in depth here, but OO-CSS is an intelligent solution to a real
problem and contains a lot of good ideas. The principle of separating the core
visual language & grid from your feature styling gives you a much more
straightforward contract between between HTML and CSS fig.2 and allows you to
confidently re-use and adapt code with less risk of unexpected consequences.
But the technique also comes with a significant sticking point - it requires
reuseable styles to be defined using explicit classnames (such as .left-
column) rather than semantic classnames (.main-content).


```css
        .column-1of2 {
            width: 50%;
            float: left;
        }

        .left-column {
            clear: both;
        }

        .highlight {
            color: red;
        }





    # Timeline







    ## Something happened!



```



![](http://static.responsivenews.co.uk/examples/img/less-css-vis1.png)

This is a problem because it blurs the boundaries between style & content. A
core part of the web-standards movement was the idea that we can insulate our
content from conflicting browser behaviour by separating it from visual
styling and behaviour. By using explicit classnames we're in effect reverting
back to the inline styles of old.

Pragmatically speaking, whilst this might be a compromise worth taking for
traditional single-view websites, but it's a big problem for [responsive
design](http://www.alistapart.com/articles/responsive-web-design/). At tablet
size your _.left-column_ div might be styled as left-hand column, but at
smartphone size it becomes full-width. Which classname would you use in that
situation; _.left-column_ & _.full-column_?

## Object Oriented CSS using LESS

3. LESS structure![](http://static.responsivenews.co.uk/examples/img/less-css-
figure3.png)

We can take the core principles of Object Oriented CSS - separating core
visual language & grid styles from HTML feature styles, but rather than
constructing these styles inline in the HTML with classnames we can use a CSS
compiler to construct feature styles at the stylesheet level. There are a few
CSS compiler languages available, but we've opted for LESS due to its
similarity to vanilla CSS syntax and because it runs on node.js (which we
already had running in our build process).

A styleguide LESS file would look like this:

```css

        .column-1of2 () {
            width: 50%;
            float: left;
        }

        .left-column () {
            clear: both;
        }

        .highlight () {
            color: red;
        }
```

We can then create a LESS file for each HTML feature we produce, each of which
simply reference the styleguide classes.


```css
        .feature-4 {
            .column-1of2;
            .left-column;

            article:first-child h2 {
                .highlight;
            }
        }
```

A LESS 'compiler' file pulls in both the styleguide and feature file:

```css

        @import (styleguide.less);
        @import (feature-4.less);

```
And outputs a CSS file that looks like this:

```css

        .feature-4 {
            width: 50%;
            float: left;
            clear: both;
        }

        .feature-4 article:first-child h2 {
            color: red;
        }





    # Feature 4







    ## Something happened!


```




fig.3. You can encourage re-use even further by creating UI patterns from your
styleguide.

Styleguide with UI pattern:

```css

        .column-1of2 () {
            width: 50%;
            float: left;
        }

        .left-column () {
            clear: both;
        }

        .highlight () {
            color: red;
        }

        .pattern-1 () {
            .column-1of2;

            article:first-child h2 {
                .highlight;
            }
        }

```
Feature:

```css

        .feature-4 {
            .pattern-1;
            .left-column;
        }
```

![](http://static.responsivenews.co.uk/examples/img/less-css-vis1.png)

This technique maintains the separation between styleguide & feature styles
that allows Object Oriented CSS to scale so well, but by retaining the
distinction between content and styling we can apply these benefits to a
responsive design. If you create parallel LESS feature files for your
different responsive views, you can simply choose different UI patterns from
the styleguide for each viewport.

Styleguide with two UI patterns:

```css

        .column-1of2 () {
            width: 50%;
            float: left;
        }

        .column-1of1 () {
            width: 100%;
            float: none;
        }

        .left-column () {
            clear: both;
        }

        .highlight () {
            color: red;
        }

        .pattern-1 () {
            .column-1of2;

            article:first-child h2 {
                .highlight;
            }
        }

        .pattern-2 () {
            .column-1of1;

            article:first-child h2 {
                .highlight;
            }
        }
```

Feature (wide viewport):

```css

        .feature-4 {
            .pattern-1;
            .left-column;
        }
```

Feature (narrow viewport):

```css

        .feature-4 {
            .pattern-2;
            .left-column;
        }
```

![](http://static.responsivenews.co.uk/examples/img/less-css-vis1.png)

![](http://static.responsivenews.co.uk/examples/img/less-css-vis2.png)

fig.4 By giving each HTML feature a LESS file for every viewport, you get a
completely modular, dry CSS framework for responsive design.

4. Responsive LESS
structure![](http://static.responsivenews.co.uk/examples/img/less-css-
figure4.png)

## …Gzip

**Remember to Gzip!** The downside of using CSS compilers in this way is that the styleguide code is duplicated every time they it gets applied to a feature. If you Gzip your files this isn't problem, because Gzipping is practically designed to work best with identical strings of text, but without Gzip those files get quite large.

