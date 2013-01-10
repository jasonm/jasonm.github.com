---
layout: post
title: "CLAHub: Easy contributor agreements on GitHub"
date: 2013-01-09 17:22
comments: true
categories: development, clahub
---

{% img right /assets/clahub/clipboard.png 150 150 %}

[CLAHub](http://clahub.herokuapp.com) is a small side project I cooked up a few
months ago, and just got around to open-sourcing.  The goal is to remove the
friction of [Contributor License Agreements](http://en.wikipedia.org/wiki/Contributor_License_Agreement)
for contributors and maintainers alike.  It's not done yet, but I'm curious to
hear what people think.

What is it?
-----------

The general idea with CLAs is this: contributors grant the maintainer a license
to distribute the their code, and state that they're legally able to do so.  A
fair number of projects have a CLA in place, including
[jQuery](http://jquery.github.com/cla.html),
[Node.js](http://nodejs.org/cla.html),
[Django](https://www.djangoproject.com/foundation/cla/), and
[Chef](http://wiki.opscode.com/display/chef/How+to+Contribute).  In the best
cases the CLA is signed via electronic signature, like
[Node.js does with a Google Form](http://nodejs.org/cla.html).  In the worst
cases you have to print, sign, and fax the agreement.  In all cases,
maintainers are responsible for cross-referencing contributions and signatures
to make sure all contributions have a corresponding signature.

With [CLAHub](http://clahub.herokuapp.com) and an open source project on GitHub
you can:

* Sign in with GitHub and create a CLA for your project.
* Ask contributors to sign in with GitHub to electronically sign the CLA.
* See on each pull request whether the contributors have all signed your CLA.
  This uses the handy [Commit Status API](https://github.com/blog/1227-commit-status-api),
  similar to what CI tools do.

[Here's the app](http://clahub.herokuapp.com/).  There's a little slideshow on
the frontpage to see how it works.  And [here's the source on GitHub](https://github.com/jasonm/clahub).

Learn more about CLAs
---------------------

Here's some more background on CLAs:

* [_Contributor License Agreements_](http://jacobian.org/writing/contributor-license-agreements/) by Jacob Kaplan-Moss.
* [_A CLA By Any Other Name_](http://www.groklaw.net/article.php?story=20110524120303815) on Groklaw.

Want to choose a CLA?  [Project Harmony](http://www.harmonyagreements.org/) is a web tool that helps you quickly select a CLA.

Feedback
-----------

There's more that needs to be done, but the core of the app works.
The [next steps are in GitHub issues](https://github.com/jasonm/clahub/issues).

Do you use a CLA for your project(s)?  Would this encourage you to add a CLA if
you don't have one already?  (That's not really my goal - just to reduce
friction where CLAs are already valuable.)  If you have a CLA, would you use
something like this to reduce the barrier to entry and your overhead?  What
kinds of features would be useful?
