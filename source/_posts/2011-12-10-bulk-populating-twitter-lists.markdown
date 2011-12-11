---
layout: post
title: "Bulk populating Twitter lists with twurl"
date: 2011-12-10 21:01
comments: true
categories: 
---

I collected a large number of Twitter usernames recently from conference websites (tryin' to bone up on a new topic) and wanted to put them all into a new list.  No UI?  No problem!  To the [Twitter API](http://api.twitter.com/1/lists/members/create_all.format) we go...

1. Create a list.  Note its URL slug (its string representation in the URL).

2. [Create a new app at the Twitter developer site](https://dev.twitter.com/apps/new).

3. After it's created, go to "Settings", scroll down, and change its access to "Read and Write":

   <img src="/images/2011_12_11_twitter_application_access.png">

   Note the consumer key and consumer secret.

4. Install [twurl](https://github.com/marcel/twurl).  It's like curl, but for Twitter:

       $ gem install twurl

5. Authorize `twurl`:

       $ twurl authorize --consumer-key your-key-goes-here \
                         --consumer-secret your-somewhat-longer-secret-goes-here

   Copy and visit the link it gives you, then paste the PIN code back into the console.

6. Add the screen names to the list.

   Replace the list slug, your screen name, and the comma separated list of screen names that will go into the list with your own values:

       $ twurl -X POST
               -d "slug=sweet-songs&owner_screen_name=jayunit&screen_name=donkeysong,brian,gabe5000"
               /1/lists/members/create_all.json

And you're done!  Go check out your list.

This could be wrapped up into a Twitter-authed app, which I'll leave as an exercise.
