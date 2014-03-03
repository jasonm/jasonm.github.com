---
layout: post
title: "Reading List: February"
date: 2014-03-02 22:07
comments: true
categories: reading-list
---

I am maintaining a reading list:

[http://jayunit.net/reading-list](http://jayunit.net/reading-list)

I'm choosing a theme per month, with several works on the theme.
I'll try to collect notes at the end of each month about what I read and wrote,
what I thought, and how I might revisit that theme in the future.

Take "reading" loosely - presentations and podcasts definitely count.

What did I complete in February?
================================

My high level goals for February were to:

* Read about [Meteor](https://www.meteor.com/),
  [FRP](http://www.haskell.org/haskellwiki/Functional_Reactive_Programming),
  and [React](http://facebook.github.io/react/).
* Do some toy projects with these.
* Skim a finance book.

Meteor
------

### What did I read?

* Read the [ Discover Meteor ](http://book.discovermeteor.com) book.
* Watched the [RealtimeConf 2012 talk](http://2012.realtimeconf.com/video/matt-debergalis).
* Listened to the [JavascriptJabber podcast episode on Meteor](http://javascriptjabber.com/076-jsj-meteor-js-with-marcus-phillips-and-fred-zirdung/).
* Understood more about Deps from the [Programming in the Reactive Style with Meteor JS slides](http://www.slideshare.net/fitc_slideshare/meteor-reactivestyle).
* Went to a [Meteor Devshop talk](https://www.meteor.com/blog/2014/02/14/pete-hunt-talks-facebook-react)
  about [React](http://facebook.github.io/react/) and [react-meteor](https://github.com/benjamn/react-meteor).
* Built a [toy chat app in Meteor](https://github.com/jasonm/toy-meteor-chat).

### Thoughts:

Lots of interesting ideas, but I'm not sure I'll use Meteor.  Maybe I'll use
parts (DDP).

* Isomorphic (same code for the client and server) is interesting.  I would have to
  build a large app to see demonstrated advantages, but can imagine.
* Seems to work best when all used together, but no reason you couldn't
  extract e.g. DDP or Deps and use in an existing application.  (Right?)
* There are three places to get packages -
  [core](http://docs.meteor.com/#usingpackages),
  [Atmosphere](https://atmosphere.meteor.com/), and,
  [as of June 2013, NPM](http://meteorhacks.com/complete-npm-integration-for-meteor.html).
  It's nice that they bless a small set of core packages as a stdlib of sorts,
  although I don't understand the need for Atmosphere and NPM to be separate.
* The [Deps](http://docs.meteor.com/#deps) module, especially `Deps.autorun`,
  is a particularly elegant approach to automatic dependency registration that
  avoids the need for static analysis.  The clever part relies
  JavaScript's single-threaded nature, and tracks the current computation
  as `Deps.currentComputation`.

  * At first I thought this could break down if you depend on a boolean
    expression `a() && b()`.  If `a()` returns `false`, the language
    short-circuits evaluation so that `b()` is never invoked, so the
    dependency is not registered.  However, once `a()` returns `true`, `b()`
    will get run and the dependency is registered correctly.  There must
    be some shortcomings?  External data is a clear example, but that is
    covered by the more explicit `Deps.Dependency` facilities.

### Questions:

* What effect does DDP and the use of a document storage model inform data
  modeling?  How would you build a system like DDP atop a relational model?
  One system I work on uses multiple steps of data mapping (SQL -> Python
  objects -> JSON -> Backbone models and back).  This seems like unnecessary
  layers of complexity, and replicating a datastore into the client, like
  minimongo does, seems like a preferable situation in many cases.  How
  might you introduce DDP to an existing rich-client application without
  rewriting it in Meteor?
* How do you build for reliability atop DDP and RPC?  E.g. ensuring all
  RPC endpoints are idempotent.  How does DDP navigate timeout/retry/backoff?
  See [Andrew Wilcox](http://awwx.ws)'s
  [ meteor-offline-data ](https://github.com/awwx/meteor-offline-data) work.
* How does operational transform (OT) fit in?
  Compare to the [ Derby ](https://github.com/codeparty/derby) framework and
  its [ Racer ](https://github.com/codeparty/racer) library, which uses
  [ShareJS](http://sharejs.org/) for OT:

  * OT is one approach to conflict resolution.  I assume there are many.  What
    are the tradeoffs?
  * What other conflict resolution approaches exist?  Maybe some in
    the [thinkdistributed.io Causality episode](http://thinkdistributed.io/blog/2013/08/28/causality.html).

    > The correctness problems of OT led to introduction of transformationless post-OT schemes, such as WOOT, Logoot and Causal Trees (CT). "Post-OT" schemes decompose the document into atomic operations, but they workaround the need to transform operations by employing a combination of unique symbol identifiers, vector timestamps and/or tombstones.
    >
    > http://en.wikipedia.org/wiki/Operational_transformation

  * If you use OT, can you use tree-structured data?
    Is the scope of OT limited to a document?  Can you coordinate operations across
    documents?

### What would I study about this next?

* How does the very new (documented February 27)
  [Blaze rendering system](https://github.com/meteor/meteor/wiki/Using-Blaze)
  compare to React?
* How well does Meteor play with other libraries?  I recall seeing a
  "modularized" version of Meteor where some parts were available a la carte.
  What does it look like to involve something like Backbone for models?  React
  for DOM computation?
* What is the multi-server story (for performance and for availability)?  I
  think that the [new oplog work](https://www.meteor.com/blog/2014/02/24/meteor-071-oplog-support-for-complex-queries-meteor-developer-accounts)
  is supposed to support this.
* [SQL bindings](https://trello.com/c/Gf6YxFp2/42-sql-support)

Functional Reactive Programming (FRP)
-------------------------------------

### What did I read?

* Read [Composing Reactive Animations](http://conal.net/fran/tutorial.htm) as
  an introduction to the concepts.
* Read [Functional Reactive Animation](http://conal.net/papers/icfp97/icfp97.pdf)
  * Read somewhat thoroughly.  General ideas of facts / behaviors over
    implementation is nice.  I learned about the idea of using integration
    to determine a value at a point in time rather than continual
    time-chunked evaluation, refreshed some of my Haskell notation,
    and thought about the general idea of declaring behaviors and letting
    the implementation handle optimizations and discretization.
* Skimmed [Push-Pull Functional Reactive Programming](http://conal.net/papers/push-pull-frp/push-pull-frp.pdf)
  * I started this, but got bogged down in notation and FP/Haskell concepts.  I
    should revisit this after reviewing Monads/Monoids/Applicatives in
    March, probably from learnyouahaskell.com.
* Listened to the [JavascriptJabber podcast episode on Functional Reactive Programming](http://javascriptjabber.com/061-jsj-functional-reactive-programming-with-juha-paananen-and-joe-fiorini/)

There are a bunch more resources [in my 2014-February reading-list page](https://github.com/jasonm/reading-list/wiki/2014-February)
that I collected but didn't get to.

### Thoughts:

Seems like a solid theoretical underpinning for complex dataflow apps.

FRP concepts (behaviors) have similarities to promises - they represent an
abstraction of a value.  Promises represent a future value.  Behaviors
represent a value which may vary continuously over time, they are functions of
time.

From reading the papers, I also learned a more general concept - that of a
paper separately introducing formal semantics from a specific implementation.
I haven't read enough CS papers to know how typical this is.

That reminds me of a [thinkdistributed.io](http://thinkdistributed.io)
podcast on Raft (a consensus algorithm) which was designed for
understandability, seemingly a novel goal in CS research.  The result is a
large number of (attempted) implementations, because the theory was so
approachable.  Does the Raft paper propose a formal semantics?  See in
May/June.

### Questions:

I'd like to try out https://github.com/baconjs/bacon.js and read about
underlying ideas.

### If I were to study further/return to this, what would I look at?

I didn't get to read too much about FRP.  I would like to try some of
the JS implementations to build some small dataflow applications.
I'd like to build some medium-sized React.js apps and compare that
experience to the FRP libraries.

React.js
========

### What did I read?

First, I read about React from other people:

* Read many of [David Nolen's articles about Om and React](http://swannodette.github.io/2013/12/17/the-future-of-javascript-mvcs/) (seriously just go read David's entire blog, it's the inspiration for so much of my reading this year)
* Grasped a key idea in React with [Eric Normand's "React: Another Level of Indirection"](http://www.lispcast.com/react-another-level-of-indirection)
* Finally got the big picture from [Pete Hunt's "React: Rethinking Best Practices"](http://2013.jsconf.eu/speakers/pete-hunt-react-rethinking-best-practices.html) talk
* Read about React's [fast tree-diff algorithm](http://calendar.perfplanet.com/2013/diff/) which underpins its virtual DOM implementation
* Read a (biased? :) comparison by Pete Hunt of [React vs Angular](http://skulbuny.com/2013/10/31/react-vs-angular/)
* Read about [Khan Academy engineers' experience of adopting React](http://joelburget.com/backbone-to-react/)
* Listened to [the JavascriptJabber podcasts episode on React](http://javascriptjabber.com/073-jsj-react-with-pete-hunt-and-jordan-walke/)

Then I react the docs themselves:

* Read the [React tutorial](http://facebook.github.io/react/docs/tutorial.html)
* Read the [React guides](http://facebook.github.io/react/docs/getting-started.html)

And aimed to get my hands dirty:

* Reviewed my friend [Brandon Tilley's article on Creating Chrome Extensions with React](http://brandontilley.com/2014/02/24/creating-chrome-extensions-with-react.html)
* Went to a [Meteor Devshop talk](https://www.meteor.com/blog/2014/02/14/pete-hunt-talks-facebook-react)
  about [React](http://facebook.github.io/react/) and [react-meteor](https://github.com/benjamn/react-meteor).
* Wrote a [negligible amount of code](https://github.com/jasonm/toy-viewport-splitter)

### Thoughts:

Looks good, I want to do more with it.

The design of React is very appealing; using functional composition, cohering
templates with view logic, implementing synthetic DOM events atop delegation,
and providing an [immediate mode atop the DOM's retained mode](http://msdn.microsoft.com/en-us/library/windows/desktop/ff684178)
Not sure on the JSX syntax, but I think I'll like it as soon as I install
[vim-jsx](https://github.com/mxw/vim-jsx).

### If I were to study further/return to this, what would I look at?

* Build something more substantial with React
* [Om](https://github.com/swannodette/om), a CLJS wrapper atop React enjoying
  advantages from immutable data structures and presumably other CLJS
  fanciness
* What does [this discussion](https://groups.google.com/forum/#!msg/clojure/XQ4wuUc0bCk/vpCxosZvbyMJ) mean by "UI as value"?

### People:

https://twitter.com/sgrove
https://twitter.com/floydophone
https://twitter.com/swannodette

Finance
-------

On my friend [Mac Cowell](https://twitter.com/100ideas)'s recommendation, I
skimmed some of [The Little Blue Book That Still Beats the Market](http://www.amazon.com/Little-Still-Market-Books-Profits/dp/0470624159).
I maintain a healthy level of skepticism, but underneath the "wow it's pure
magic!" skin appears to be a proxy for value investing that identifies
underpriced companies.  Time will tell if broad dissemination of this
valuation strategy will correct the underpricing, or if it holds.

I'd like to, as an exercise, build a software implementation of this strategy
and backtest it.  I'd also like to understand if there is affordable
historical market information that avoids
[survivorship bias](http://en.wikipedia.org/wiki/Survivorship_bias), and to understand what
other [backtesting blind spots](http://seekingalpha.com/article/363901-the-perils-of-backtesting-technical-strategies)
I may have.

I'll follow this up (also on Mac's recommendation) with Graham and Zweig's
[The Intelligent Investor](http://www.amazon.com/The-Intelligent-Investor-Definitive-Investing/dp/0060555661),
maybe in March or April.

Links from an hour-ish of searching about building trading simulations:

* Data sources:
  * [StackOverflow: "algorithmic trading simulator/benchmark data"](http://stackoverflow.com/questions/5774218/algorithmic-trading-simulator-benchmark-data)
    * [Market Archive](https://market-archive.appspot.com/) - no idea of
      trustworthiness
    * [Caltech Quantitative Finance Group](http://quant.caltech.edu/historical-stock-data.html)
  * [Survivorship bias](http://en.wikipedia.org/wiki/Survivorship_bias)
* Tools:
  * [Quantopian](https://www.quantopian.com)
    * [Can I use fundamental data in Quantopian?](https://www.quantopian.com/posts/using-the-fetcher-with-quandl)
  * [PyAlgoTrade](http://gbeced.github.io/pyalgotrade/)
  * [Interactive Brokers' toolset](https://www.interactivebrokers.com/en/?f=tws&p1=papertrader) - Quantopian is privately testing IB API integration, so that's promising

Closing Thoughts
================

There was only one time previously when I was this organized about my reading.
Two summers ago, on vacation in Germany during [a year abroad](http://jayunit.net/2012/12/31/a-year-of-travel/), I had plenty of
leisure time to fill.  I decided to schedule a few hours each day of German
language lessons on [Duolingo](https://www.duolingo.com/), reading about
biotechnology, and reading about education technology, taking notes the whole
way.  It felt immensely productive, but I was't able to keep up with it when
we started traveling again.

The pace for this month was quite high, but I enjoyed keeping up with it.
I've found it helpful to schedule reading nights on my calendar to try
and block some time off for paying attention.

The part I have enjoyed the most is discussing these ideas with others.
I'd like to try small reading groups or journal clubs around some of my
future readings.
