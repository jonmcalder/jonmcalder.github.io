---
layout: post
title: Naming things is hard
subtitle: Prefixing R function names
share-img: "http://raw.githubusercontent.com/jonmcalder/jonmcalder.github.io/master/img/small-imgs/r_exercism_icon.png"
tags: [rstats, R, exercism, open source, programming]
---

> ‘There are only two hard things in Computer Science: cache invalidation and naming things.’

The above quip by Phil Karlton is fairly well known and often quoted, sometimes with amusing extensions:

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">There are two hard things in computer science: cache invalidation, naming things, and off-by-one errors.</p>&mdash; Jeff Atwood (@codinghorror) <a href="https://twitter.com/codinghorror/status/506010907021828096?ref_src=twsrc%5Etfw">August 31, 2014</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">There are only 2 hard things in computer science:<br>0. Cache invalidation<br>1. Naming things<br>7. Asynchronous callbacks<br>2. Off-by-one errors</p>&mdash; Paweł Zajączkowski (@gvaireth) <a href="https://twitter.com/gvaireth/status/909805115707727873?ref_src=twsrc%5Etfw">September 18, 2017</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

These are funny, but they do also convey some truth: in the midst of all the technicalities and abstractions we can find ourselves caught up with in the world of programming, it's surprising how often seemingly 'simple' things like naming things trip us up.

I was recently reminded of this when a difference of opinions about function names sparked some healthy debate in a pull request I was reviewing for one of my personal projects.

So the question I want to raise is this:

> "When (if ever) is it a good idea to adopt a prefixing convention for the names of exported functions in an R package?"

## Disclaimers

Before I dive into the details I feel it is important to state a few things upfront.

Firstly, I want to thank my friends and collaborators Katrin and Lorenz who are strong proponents of open source software and for whom I have a lot of respect. On this occasion they both seem to disagree with me, but that is not a bad thing - discussion and debate is valuable, and that's not gonna happen when everyone agrees with each other all the time. I did also ask for their permission before publishing this post.

![xkcd: Survivorship Bias](https://imgs.xkcd.com/comics/survivorship_bias.png "xkcd: Survivorship Bias"){:style="float:right;margin-right: 15px;margin-top: 15px;margin-left: 15px;margin-bottom: 15px;width:300px;"}

Secondly, my purpose in writing about this is less about trying to determine who is right, and more about attempting to convert this experience into insight. Often we learn more from our failures than from our successes, but it's harder to share our mistakes than it is to share our triumphs. So this post is my way of being vulnerable about something that is a work in progress and the process of trying to improve on it. As an aside, if you haven't encountered survivorship bias before, I highly recommend you [read this](https://youarenotsosmart.com/2013/05/23/survivorship-bias/).

Thirdly, I was wrong. This is especially important in light of the previous point. I was wrong to raise the issue of function names (for the package as a whole) in a pull request which was focused on something else. This is a valuable lesson. One should always aim to keep pull requests (and issues) narrow in scope because attempting to tackle multiple problems in one place (unless they are inherently linked or dependent on each other) is messy and complicates matters unnecessarily.

Lastly, what I share will be my own opinion, but it is just an opinion and I'm always open to learning from others with different views. My hope is that collectively we can share some worthwhile perspectives from both sides and possibly encourage more thinking and conversation around this or related issues.

## Background Context

Ok, so having made those upfront disclaimers, I'll begin by summarizing the back-story and context in which the discussion arose. If you'd like to refer to the pull request itself - it can be found [here](https://github.com/jonmcalder/exercism/pull/9#pullrequestreview-66774136).

![R track on Exercism](http://jonmcalder.github.io/img/small-imgs/r_exercism_icon.png "R track on exercism"){:style="float: left;margin-right: 15px;margin-top: 15px;margin-left: 15px;margin-bottom: 15px;width: 200px;"}

[excercism.io](http://exercism.io/) is a learning platform that aims to provide code practice and mentorship for everyone. I got involved in developing the R track on Exercism and wrote about it [earlier this year](http://joncalder.co.za/2017-04-05-r-track-on-exercism/). Unlike most online learning platforms, with Exercism, all the coding happens on your machine in an environment you're familiar with. So Exercism provides a command line tool which leverages an API in order to facilitate the process of fetching and submitting exercises.

A few months ago, I had an idea to write an R package which wraps this API. The thinking was that the user experience (for R users) might be improved upon by facilitating interaction with `exercism.io` directly from R itself. This removes the need for switching repeatedly between R and a shell when fetching, iterating on and submitting exercises - although now the addition of terminal tabs in RStudio 1.1 has already reduced this friction to a degree. In any case, there are additional opportunities for Exercism helper functions in the package which can be context aware and integrate with the RStudio if it is being used. An example this could be functions (or addins) which make use of the `rstudioapi` to detect which problem was last worked on when submitting so that it doesn't need to be specified manually.

Katrin, who is a co-maintainer for the R track on exercism.io, has also been collaborating on this R package with me and has had some great ideas like leveraging [testthat's](http://testthat.r-lib.org/) `auto_test()` to facilitate and encourage test driven development, as this is one of the implicit goals of Exercism. In the PR introducing this feature, the potential for function name confusion was soon evident when this new Exercism specific version of `testthat::auto_test()` was (initially) given the name `autotest()`. This reminded me that I'd in fact been thinking for a while about renaming all the exported functions to adopt the prefixing convention `ex_*` (for a few different reasons which I'll get to later). So I figured this "name clash" was as good a catalyst as any, and made the suggestion to start adopting the new naming convention in the PR. Once again it's worth noting that this was a mistake - I should have instead opened a separate issue to discuss my proposed change in naming conventions.

## Discussion & follow-up

The suggestion was met with some resistance, and after some further discussion it became clear to me that it was a thoughtfully considered resistance. So I asked my friend Lorenz to weigh in on the discussion too, given that he knows Katrin and I but is not involved in the project and thus has the benefit of a more neutral perspective. To my surprise, he did not agree with me either!

But I did still seem to have [Jenny Bryan on my side](https://github.com/jonmcalder/exercism/pull/9#issuecomment-334316542) (thanks Jenny!), and I figured [Hadley](https://twitter.com/hadleywickham) (or Sir Lord General Professor Wickham as [Mara](https://twitter.com/dataandme) generally likes to refer to him) had to have thought it was a good idea at some point given the `str_` prefix for [stringr](http://stringr.tidyverse.org/) and `fct_` prefix for [forcats](http://forcats.tidyverse.org/) among others. So after thinking on the problem for a while, out of curiousity I eventually tweeted out a poll to see if I could get any sense of where the `#rstats` community falls on this issue.

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">What is your take on prefixing conventions for <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> function names? (e.g. stringr/stringi, forcats, googlesheets)</p>&mdash; Jon Calder (@jonmcalder) <a href="https://twitter.com/jonmcalder/status/921470775294681089?ref_src=twsrc%5Etfw">October 20, 2017</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

At a glance it looks like a reasonable proportion are actually in favour of prefixing conventions for function names (or at least not against the idea), but of course there are a number of disclaimers to make here:

- Character limits (at the time of the poll) made it hard to communicate the question clearly or to include any additional context for the question, so that probably leaves a lot of room for interpretation
- I don't have much reach on Twitter, so there weren't many responses (81 votes is not much to go on)
- Even if there had been a good number of responses, Twitter polls need to be looked at skeptically given the potential for sampling bias
- Speaking of sampling bias, most of the votes came in after Hadley tweeted a reply to the poll so it makes sense that the results would be skewed towards his legions of followers (I'm one of them and the degree of influence is clear because his packages are what got me considering prefixing conventions in the first place, among others like [googlesheets](https://github.com/jennybc/googlesheets))

[Maëlle](https://twitter.com/maelle) had two helpful follow-ups for me. Firstly, she encouraged me to blog about this (and I don't think I would have done so otherwise so thanks Maëlle!). Secondly, she directed me to the ROpenSci review process for her package [ropenaq](http://ropensci.github.io/ropenaq/), which provides access to air quality data via the OpenAQ API. In his [review of the package](https://github.com/ropensci/onboarding/issues/24#issuecomment-177408867), [Andrew MacDonald](https://twitter.com/polesasunder) suggested the following:

> "I was thinking that the names of functions might be a bit similar to functions in other packages that also use geography. What do you think of prefixing every function with a package-specific string? Perhaps something like aq_ before all the user-facing functions (i.e. countries() becomes aq_countries()). This is similar to another rOpenSci package, geonames, which uses GN (as in GNcities()). This has the added benefit of playing very nicely with Rstudio, which will show all the package functions as completions when users type aq_ and hit tab."

Interestingly, this suggestion (although the original inspiration may have come from elsewhere) was later incorporated into [ROpenSci's packaging guide](https://github.com/ropensci/onboarding/blob/master/packaging_guide.md#funvar):

![ROpenSci logo](http://jonmcalder.github.io/img/small-imgs/ropensci_logo.png "ROpenSci logo"){:style="float: right;margin-right: 15px;margin-top: 15px;margin-left: 15px;margin-bottom: 15px;width: 200px;"}

> Consider an object_verb() naming scheme for functions in your package that take a common data type or interact with a common API. object refers to the data/API and verb the primary action. This scheme helps avoid namespace conflicts with packages that may have similar verbs, and makes code readable and easy to auto-complete. For instance, in `stringi`, functions starting with `stri_` manipulate strings (`stri_join()`, `stri_sort()`, and in `googlesheets` functions starting with `gs_` are calls to the Google Sheets API (`gs_auth()`, `gs_user()`, `gs_download()`).

Though I hadn't seen this recommendation from ROpenSci at the time, it aligns very strongly with my initial reasoning for wanting to change the function names in the `exercism` package. It is primarily an API package, and all functions either interact with the exercism.io API or act on some (local) Exercism data/code (exercises). A potential objection could be that in some cases the `ex_*` prefix may be interpreted either as `exercism_*` or as `exercise_*`, but I don't think that's a problem since either way the context is common and shared implicitly.

Having said that, I'm also aware that a prefixing convention is not suitable in the majority of cases and there are reasons to avoid it, otherwise it would already be far more common. I've not tried to summarize the arguments for and against it here since this post is already quite lengthy, but I believe Katrin and Lorenz both raised a number of good points over in the [original PR thread](https://github.com/jonmcalder/exercism/pull/9#issuecomment-334929991), so I would encourage you to read through that to get some more insight into the potential pros and cons.

Below is an overview of the currently exported functions for `exercism`, along with a brief 
description of what they do and potential new names for each should we adopt a prefixing convention:

| Current Function | Description | New Name? |
| -- | -- | -- |
| `set_api_key()` | Set an environment variable for the provided exercism.io API key, and store in .Renviron so that it can persist for future sessions. | `ex_set_key()` |
| `set_exercism_path()` | Set an environment variable for the provided exercism path, and store in .Renviron so that it can persist for future sessions. | `ex_set_path()` |
| `track_status()` | Fetches current track status from exercism.io | `ex_status()` |
| `check_next_problem()` | Returns the next problem for a language track | `ex_check()` |
| `fetch_problem()` | Fetches the files for a problem via the Exercism API and writes them into a new problem folder in the Exercism directory | `ex_fetch()` |
| `fetch_next()` | Checks for the next problem via the Exercism API, and writes the files into the folder in the Exercism directory | *special case of `ex_fetch()` |
| `open_exercise()` | Open files for an exercism.io problem | `ex_open()` |
| `start_testing()` | Exercism- and R-specific wrapper for `testthat::auto_test()` that starts testing your solution against the problem's test cases. | `ex_auto_test()` |
| `submit()` | Submits the specified solution to exercism.io | `ex_submit()` |
| `skip_problem()` | Marks a problem as 'skipped' via the Exercism API | `ex_skip()` |
| `browse_exercise()` | Navigate to an exercise description on exercism.io | `ex_browse()` |
| `browse_solution()` | Navigate to an exercise solution on exercism.io | *special case of `ex_browse()` |

So looking at the above, do you think this a good use case for an `object_verb()` naming convention? How should one determine this? Please feel free to comment with your thoughts and suggestions below or ping me on [Twitter](https://twitter.com/jonmcalder).
