---
layout: post
title: "Backbone.js on Rails talk"
date: 2011-09-25 22:40
comments: true
categories: 
---

On Tuesday, September 20, I gave a talk at the
[New Hampshire Ruby Users Group](http://nhruby.org/) on Backbone.js on Rails.
I'll be giving a very similar talk on Tuesday, October 11 at
[boston.rb](http://bostonrb.org/presentations/backbonejs-and-rails) and a
version more targeted to front-end developers on Wednesday, October 26 at the
[Boston Front End Developers meetup](http://www.meetup.com/Boston-Frontend-Developers/)

I have posted the [Backbone.js on Rails slides](http://jayunit.net/backbone-js-on-rails-talk/)
online, and the [slide source is on my GitHub](https://github.com/jasonm/backbone-js-on-rails-talk/tree/gh-pages).

As an aside, I'm using [landslide](https://github.com/adamzap/landslide) for the
slides - I love the resulting HTML and interface, though I've heard great things
about [deck.js](http://imakewebthings.github.com/deck.js/).

People found the resources sections useful. Many of the links are buried in the
presenter notes, so I'll repeat them here.  There are plenty more online, and
I'm sure I'm missing some content.  Please link to any of your favorites in the
comments, and I'll add them.

### Testing

* Isolation test with [Jasmine](http://pivotal.github.com/jasmine/):
    * Spy/stub/mock, even your HTTP, with [sinon.js](http://sinonjs.org/)
    * If you're looking for factory_girl.js, it's called [Rosie](https://github.com/bkeepers/rosie)
    * [guard-jasmine](https://github.com/netzpirat/guard-jasmine) autotest your Jasmine with headless webkit ([phantomjs](http://www.phantomjs.org/))
    * Write in CoffeeScript and use the 3.1 asset pipeline with [jasminerice](https://github.com/bradphelan/jasminerice)
    * Get started with James Newbery's excellent blog posts on [testing Backbone with Jasmine](http://tinnedfruit.com/2011/03/03/testing-backbone-apps-with-jasmine-sinon.html)
    * Check out his [examples on GitHub](https://github.com/froots/backbone-jasmine-examples)
* Integration test with:
    * [capybara-webkit](https://github.com/thoughtbot/capybara-webkit) for fast, headless, accurate WebKit testing
    * Selenium for other browsers, or if capybara-webkit has issues.

### Push synchronization

* Rails `Model#save` cascades to clients: [backbone_sync-rails](https://github.com/jasonm/backbone_sync-rails) over pubsub bus [Faye](http://faye.jcoglan.com/)
* Work-in-progress [Software transactional memory](http://en.wikipedia.org/wiki/Software_transactional_memory) sync: [https://github.com/codeparty/racer](https://github.com/codeparty/racer)
    * Future plans: [diff-match-patch](http://code.google.com/p/google-diff-match-patch/), [Operational transform](http://en.wikipedia.org/wiki/Operational_transformation)
* [Now.js](http://nowjs.org/)
* [Substack DNode](https://github.com/substack/dnode)
* [Data.js](http://substance.io/michael/data-js): Data Manipulation and Graph Persistence for Node.js and the Browser.  Can ride now.js transport.
    * substance.io, above, is written with Backbone.js, and is open source [https://github.com/michael/substance](https://github.com/michael/substance)
* [Backbone on the server with node.js](http://andyet.net/blog/2011/feb/15/re-using-backbonejs-models-on-the-server-with-node/)... with DNode or NowJS (?!)


### Get started with Backbone

* [Todo App example](http://documentcloud.github.com/backbone/#examples-todos)
* [James Newbery's jasmine examples](https://github.com/froots/backbone-jasmine-examples/tree/master/public/javascripts)

### Further reading: Books on JavaScript

* [JavaScript: The Good Parts](http://shop.oreilly.com/product/9780596517748.do) by Douglas Crockford
* [JavaScript Web Applications](http://shop.oreilly.com/product/0636920018421.do) by Alex MacCaw (Spine.js author)
* [Test-Driven JavaScript Development](http://tddjs.com/) by Christian Johansen
* [JavaScript Patterns](http://shop.oreilly.com/product/9780596806767.do) by Stoyan Stefanov
* [JavaScript: The Definitive Guide](http://shop.oreilly.com/product/9780596805531.do) by David Flanagan

### Further reading: Online resources

* [Official Backbone docs](http://documentcloud.github.com/backbone/)
* [Annotated source code](http://documentcloud.github.com/backbone/docs/backbone.html)
* [Underscore docs](http://documentcloud.github.com/underscore/) and [source](http://documentcloud.github.com/underscore/docs/underscore.html)
* [Backbone Google Group](https://groups.google.com/group/backbonejs)
* [Backbone on Rails eBook](http://workshops.thoughtbot.com/backbone-js-on-rails?utm_source=jm-talk)
* [Peepcode episodes on Backbone](http://peepcode.com/products/backbone-js)
