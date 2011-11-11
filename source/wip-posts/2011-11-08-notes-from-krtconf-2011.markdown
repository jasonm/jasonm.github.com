---
layout: post
title: "Notes from KRTconf 2011"
date: 2011-11-08 15:08
comments: true
categories: conference, node.js, realtime, javascript, ruby
---

### Header here with a [text](http://link)

> Blockquote

### Sunday night

Met Matt and Sim, writing Word Wars, an AIR mobile game, sells best in Nook
store surprisingly!

Guillermo socket.io author/maintainer
Paul from Pachube

Sim says a challenge is building the RESTful backend on node - it's great for
async but not great for simple restful -- for that, Rails/Django is easier, so
it's still a PITA to build a full JS stack

Guillermo ...well, see Monday talk, but generally challenges are websockets over
3G - negotiatinng buffering, even doing it at all, some networks block it.
Feature detection works for devices but not on e.g. intermediate proxies.
Also, starting with a slow-but-safe implementation and then negotiating up isn't
necessarily a good approah, since often it's an application's initial data
payload which is heaviest, so you're possibly compromising network speed at the
most critical time.

Paul at Pachube - talk to him about science and instruments.  (Later, I did
this, he's less in touch with the bizdev side and, though he'd love to see more
scientific instruments...)

### Monday morning

Breakfast chat

Now.js, transitive.io is similar
"Eight fallacies of distributed computing"
BISOSH (?) - XMPP over HTTP
buddycloud - federated  social networking on XMPP
Julien - superfeedr - interesting approach of correlating feed info to reduce
polling latency.  E.g. in a news org, when a post comes up in a topic feed, look
at the author, then update the corresponding author feed.

Challenge: Asynchronous testing.  Wanting to test at all.  But, really, async
tesing.  Also, Astro @ buddycloud says testing network protocols is hard.

#### Julien: PubSubHubBub at Superfeedr.

To make the web itself realtime, we need push everywhere.

Outline:
* Client-to-server is not enough.
* Realtime is push
* Feeds and HTTP: a reality check
* What happends next

Q: What can we push?  HTML (dirty but ubiquitious), API info (rich but
unstandard), RSS/ATOM (ugh XML but widely available and rich enough)

PSHB is pragmatic, but not perfect.  There are 3 participants: Publishers, Hubs,
and Subscribers.

Demo 1: Tumblr (P) -> superfeedr (H) -> Google reader (S)
Demo 2: Rebuilding feeds: "Track", a "mention" feed at superfeedr. Like Google alerts.
* Publish something about "krtconf", see it come up.
Demo 3: msgboy.com: Browser extension, push data to end user.  Cool: as an
extension, it sees your browsing activity, and can extract/identify feeds to
auto-subscribe you to content of interest.

#### Khris Loux - Turning Realtime into Real Revenue

vvvvvvvvvvvvvvvvvvvvvvvvvvvv
TODO: NEXT HERE
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

### Mon night

Chatted with Kyle Drake, tacos at Chipotle by Twilio.

Games at Ground Kontrol

Idea: Conferences are to build community.  What makes a community?  Is "KRT" a
community?  Versus e.g. Node, Ruby?


