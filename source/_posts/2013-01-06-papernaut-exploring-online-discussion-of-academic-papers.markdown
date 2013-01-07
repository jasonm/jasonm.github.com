---
layout: post
title: "Papernaut: Exploring online discussion of academic papers"
date: 2013-01-06 09:00
comments: true
categories: development, papernaut
---

{% img right /assets/papernaut/astronaut.png 150 150 %}

If you regularly read scholarly papers, you likely use a
[reference manager](http://en.wikipedia.org/wiki/Reference_management_software) to
maintain your personal library.  [Papernaut](http://www.papernautapp.com)
connects to your library to find online coverage and discussion of your papers
in blogs, forums, and mainstream media.  My hope is that these discussions can
provide broader perspective on research and, in some cases, be the spark that
starts a new collaboration.

Here's a very quick video demo. We start with a [Zotero](http://zotero.org)
library that includes a paper from Science on the
[effect of pesticides on honey bees](http://www.sciencemag.org/content/336/6079/348.short).
We then connect to Papernaut, and find several discussions and articles,
including one in [The Guardian](http://www.guardian.co.uk/science/grrlscientist/2012/may/08/1):

<iframe width="560" height="349" src="http://www.youtube.com/embed/ACw3iLLsSXw?rel=0" frameborder="0" allowfullscreen></iframe>

I've been working on Papernaut in my spare time for a few months, and I'm happy
to say that it's now open source.  The project comes in two parts, and the
source is on GitHub:

* [Papernaut-frontend](https://github.com/jasonm/papernaut-frontend) is the web frontend.
* [Papernaut-engine](https://github.com/jasonm/papernaut-engine) is the feed crawler and matching backend.

If you are interested in how the application is put together, the rest of this
article is a technical overview of the moving parts and how they interact.

## Overview: A simple example

Let's walk through a simplified example.  Say I have only one paper in my
reference manager -- that paper from earlier, about the effect of pesticides on
honey bees:

Henry, M., Beguin, M., Requier, F., Rollin, O., Odoux, J., Aupinel, P., Aptel,
J., Tchamitchian, S., & Decourtye, A. (2012).
[A Common Pesticide Decreases Foraging Success and Survival in Honey Bees.](http://www.sciencemag.org/content/336/6079/348.short)
_Science_, 336 (6079), 348-350 DOI:[10.1126/science.1215039](http://dx.doi.org/10.1126/science.1215039)

Let's also say that the engine is crawling content from only one source feed,
[ResearchBlogging.org](http://researchblogging.org).
Among many other content items, that source feed contains a
[relevant entry](https://researchblogging.org/post-search/list?advanced=true&post_title=&journal=&blog_blogger_name=GrrlScientist&tags=&tag_id=&search_text=&from_date=05%2F08%2F2012&to_date=05%2F08%2F2012&send=Search),
whose [content page is on The Guardian](http://www.guardian.co.uk/science/grrlscientist/2012/may/08/1).

We'll look at how the engine crawls and indexes this source feed.  Then,
we'll see how the frontend pulls the paper from my reference manager and
asks the engine for relevant discussions.

## Papernaut-engine: Loading content and identifying papers

The goal of the engine is to produce a collection of `Discussion` records, each
of which links to several `Identifier` records, representing journal papers
that are referenced from the `Discussion`.  In our example, the `Discussion` is
the article in The Guardian, and the relevant `Identifier` is
`DOI:10.1126/science.1215039`.  There are also intermediate objects, `Page` and
`Link` which connect `Discussion`s to `Identifier`s.

The engine consists of two main parts: loaders (which are Ruby classes), and the
query API (a Rails app).  For loading, it also depends on an external running instance
of the [Zotero translation-server](https://github.com/zotero/translation-server).

![](/assets/papernaut/diagram-engine.png)

### Loading content by crawling feeds

The loaders load discussion candidates from feeds and archives, extract
outbound links, and store these in the database.

In the first step, I invoke the [ResearchBlogging.org](researchblogging.org)
loader to crawl and index the most recent 100 pages of their archives:

    [engine] rails runner "Loaders::ResearchbloggingWebLoader.new(100).load"

This will load a large number of `Discussion` entries into the database, with
zero or more `Page` entries for each `Discussion`, corresponding to outbound
links.

At this point, the engine database contains the `Discussion`:

    #<Discussion id: 3424,
                 url: "http://www.guardian.co.uk/science/grrlscientist/2012/may/08/1",
                 title: " Bee deaths linked to common pesticides | video | G...", ...>

and the linked `Page` entries:

    [#<Page id: 7531, url: "http://dx.doi.org/10.1126/science.1215039", ... >,
     #<Page id: 7532, url: "http://pubget.com/doi/10.1126/science.1215039", ... >,
     #<Page id: 7533, url: "http://dx.doi.org/10.1126/science.1215025", ... >,
     #<Page id: 7534, url: "http://pubget.com/doi/10.1126/science.1215025", ... >]


### Identifying papers via the Zotero translation-server

The engine determines which outbound links (or `Page`s) are academic papers by
issuing calls to the
[Zotero translation-server](https://github.com/zotero/translation-server) HTTP API.
The translation-server is a third-party project from open-source reference
manager [Zotero](http://www.zotero.org/). It examines a given URL and, if
that page contains an academic paper, it returns common publication identifiers such as
[DOI](http://en.wikipedia.org/wiki/Digital_object_identifier) or
[PMID](http://en.wikipedia.org/wiki/PubMed#PubMed_identifier).

The translation-server wraps the
[Zotero translators](https://github.com/zotero/translators),
a set of JavaScript scripts that do the heavy lifting of parsing a webpage and
attempting to identify it as one or more academic publications.  These
translators are
[maintained by the community](https://github.com/zotero/translators/issues),
keeping them fairly up-to-date with publishers.  The translation-server uses
[XULRunner](https://developer.mozilla.org/en-US/docs/XULRunner) to run these
scripts in a Gecko environment, and makes them available through a simple HTTP
API:

    [~] ~/dev/zotero/translation-server/build/run_translation-server.sh &
        zotero(3)(+0000000): HTTP server listening on *:1969

    [~] curl -d '{"url":"http://www.sciencemag.org/content/336/6079/348.short","sessionid":"abc123"}' \
         --header "Content-Type: application/json" \
         http://localhost:1969/web | jsonpp

        [
          {
            "itemType": "journalArticle",
            "creators": [
              { "firstName": "M.", "lastName": "Henry", "creatorType": "author" },
              { "firstName": "M.", "lastName": "Beguin", "creatorType": "author" },
              { "firstName": "F.", "lastName": "Requier", "creatorType": "author" },
              { "firstName": "O.", "lastName": "Rollin", "creatorType": "author" },
              { "firstName": "J.-F.", "lastName": "Odoux", "creatorType": "author" },
              { "firstName": "P.", "lastName": "Aupinel", "creatorType": "author" },
              { "firstName": "J.", "lastName": "Aptel", "creatorType": "author" },
              { "firstName": "S.", "lastName": "Tchamitchian", "creatorType": "author" },
              { "firstName": "A.", "lastName": "Decourtye", "creatorType": "author" }
            ],
            "notes": [],
            "tags": [],
            "publicationTitle": "Science",
            "volume": "336",
            "issue": "6079",
            "ISSN": "0036-8075, 1095-9203",
            "date": "2012-03-29",
            "pages": "348-350",
            "DOI": "10.1126/science.1215039",
            "url": "http://www.sciencemag.org/content/336/6079/348.short",
            "title": "A Common Pesticide Decreases Foraging Success and Survival in Honey Bees",
            "libraryCatalog": "CrossRef",
            "accessDate": "CURRENT_TIMESTAMP"
          }
        ]

There are several useful standardized identifiers here - DOI, URL, and ISSN.

So, continuing with our example from above, I'll next start the Zotero
translation server and identify the pages:

    [engine] ~/dev/zotero/translation-server/build/run_translation-server.sh &
             zotero(3)(+0000000): HTTP server listening on *:1969

    [engine] rails runner "ParallelIdentifier.new(Page.unidentified).run"

The engine issues calls to the translation-server and records new `Identifier`s.
Now, the `Page` entries we previously crawled:

    [#<Page id: 7531, url: "http://dx.doi.org/10.1126/science.1215039", ... >,
     #<Page id: 7532, url: "http://pubget.com/doi/10.1126/science.1215039", ... >,
     #<Page id: 7533, url: "http://dx.doi.org/10.1126/science.1215025", ... >,
     #<Page id: 7534, url: "http://pubget.com/doi/10.1126/science.1215025", ... >]

have corresponding `Identifier` records:

    [#<Identifier id: 1819, page_id: 7531, body: "DOI:10.1126/science.1215039" ...>,
     #<Identifier id: 1820, page_id: 7531, body: "URL:http://www.sciencemag.org/content/336/6079/348" ...>],
     #<Identifier id: 1821, page_id: 7533, body: "DOI:10.1126/science.1215025" ...>,
     #<Identifier id: 1822, page_id: 7533, body: "URL:http://www.sciencemag.org/content/336/6079/351" ...>,

Two of the four pages were identified (`7531` and `7533`), and both of those
pages received two identifiers apiece.  This means that the Guardian `Discussion`
actually referenced two different papers, not just the one we're interested in.

Now that there is a link between the paper in question and this discussion page,
we are ready to visit the frontend.

## Papernaut-frontend: importing libraries, finding discussions

The frontend works in two distinct phases: first, it helps you import papers
from your reference manager.  Second, it shows you discussions for those papers.

You can import your papers via the
[Zotero API](http://www.zotero.org/support/dev/server_api/read_api) or
[Mendeley API](http://dev.mendeley.com/) by giving Papernaut access to your
libraries via OAuth.  This happens with
[`omniauth-zotero`](https://github.com/jasonm/omniauth-zotero) and
[`omniauth-mendeley`](https://github.com/fractaloop/omniauth-mendeley)
libraries, followed by the
[`ZoteroClient`](https://github.com/jasonm/papernaut-frontend/blob/2013-01-06/lib/zotero_client.rb) and
[`MendeleyClient`](https://github.com/jasonm/papernaut-frontend/blob/2013-01-06/lib/mendeley_client.rb)
classes.

Alternatively, you can import papers from most reference management software by
exporting and uploading a [`.bibtex`](http://en.wikipedia.org/wiki/BibTeX)
file.  Papers and their identifiers are then extracted with the
[`BibtexImport`](https://github.com/jasonm/papernaut-frontend/blob/2013-01-06/app/models/bibtex_import.rb)
class.

Many papers will have multiple identifiers, and the frontend attempts to clean and validate
your papers' identifiers as best it can in an attempt to find the best matches.

Once your papers are loaded into the frontend, it
[issues requests to the `papernaut-engine` query API](https://github.com/jasonm/papernaut-frontend/blob/2013-01-06/lib/discussion.rb)
to find discussions that match papers in your library.

![](/assets/papernaut/diagram-frontend.png)

The interface between the frontend and the engine are `Identifier` strings,
which take a type/value form:

* `DOI:10.1038/nphys2376`
* `ISSN:1542-4065`
* `PMID:10659856`
* `URL:http://nar.oxfordjournals.org/content/40/D1/D742.full`

So, in our example video above, we authenticate via Zotero and authorize
Papernaut's API access via OAuth.  The frontend extracts our library of papers
from Zotero and stores their `Identifier`s locally.  It issues requests to the
engine's query API for matching discussions, and displays those to the end
user:

![](/assets/papernaut/frontend-screenshot.png)

## Deployment

In production, the Papernaut engine and frontend are deployed to
[Heroku](https://heroku.com).  The translation-server is deployed to EC2.
I spin it up and run the loaders periodically, to reduce hosting overhead.

There is a `DEPLOY.md` file for both
[the frontend](https://github.com/jasonm/papernaut-frontend/blob/master/DEPLOY.md)
and [the engine](https://github.com/jasonm/papernaut-engine/blob/master/DEPLOY.md)
that goes into further detail.

## Next steps

I'm excited to see what kinds of results people get with Papernaut,
but it's still very early software.  I look forward to making a variety
of improvements.

I'd really like to add a bulk request API endpoint to the engine, so that the
frontend can discover discussions in a single HTTP request, rather that one
request per paper. That's a big performance hit, and the user experience right
now for large libraries is that the frontend just hangs for a while.

On the engine side, I'd like to do a better job of culling false positives in
the matching engine, and of contributing to Zotero's translators to improve the
match rate.  I think the primary issue there is that the translator-server
actually only runs a subset of all the Zotero translators, as some declare that
they only work inside a real browser context
([see "browserSupport"](http://www.zotero.org/support/dev/translators)).

I'd like to get a larger sample set of BibTeX files to try, as there are
probably edge cases and assumptions in the importer waiting to be hit.

I'd also like to background some of the tasks in the frontend's import process;
validating DOIs is a big one there.  Ideally, the whole library import would be
backgrounded, and the user interface would be notified when the import is
complete.

Currently, some matches are missed because the engine and frontend have
different identifiers for the same paper - say a DOI and a PMID.  I also have
an experimental branch that cross-references papers with the
[crossref.org API](http://help.crossref.org/#author_title_query),
which yields more complete information.  Ideally that would happen in the engine.
I've also seen some library management and import tools that use Google Scholar
to improve matching and identification.

After that, I'd like loaders to run semi-continuously instead of manually, and
to have more robust infrastructure around paper identification.

In the long term, it would be interesting to try and bring the discussion
matching experience directly into reference managers.  This is one reason why
I provide the engine query API separately from the frontend.

## Conclusion

I'm most interested in hearing feedback from people.  Is this useful to you?
If you use a reference manager,
[give Papernaut a spin](http://www.papernautapp.com)
and [let me know](mailto:jason.p.morrison@gmail.com) how it goes.
