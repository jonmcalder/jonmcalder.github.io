---
layout: post
title: Introducing hugo!
subtitle: Fun with bot frameworks
tags: [rstats, bots, slack]
---

Roughly two months ago, I embarked on a short mission: to setup an instance of [hubot](https://hubot.github.com/) for integration with Slack. Despite being 
armed with only a very limited working knowledge of Javascript (hubot 
runs on `node.js` and scripts are written in `CoffeeScript`), the tutorials 
seemed pretty thorough and made the setup process look do-able, so I decided to 
have a crack at it.

![Hubot avatar](/img/small-imgs/hubot_avatar.png "Hubot avatar"){:style="float: right;margin-right: 10px;margin-top: 10px;margin-left: 10px;margin-bottom: 10px;"}

I'll begin with my inspiration for doing this, and then provide an 
overview of the setup process (which I suggest you skip over if you're not 
interested in the details), followed by some examples of the few things I 
implemented with my instance of hubot at the outset. In case you haven't yet 
deduced it from the title of this post, his name is Hugo.

### Motivation

As I mentioned in my [previous post](/2016-12-08-breaking-the-silence), Lorenz 
and I have been collaborating on some `#rstats` projects over the past few 
months and very early on we started making use of [Slack](https://slack.com/), 
also using it's Github & Trello integrations which both involve bots.

> Sidenote: If you are involved in any form of regular collaboration and haven't
> already had a look at Slack I highly recommend you do. We've found it to be 
> hugely valuable for facilitating communication, and also a lot of fun to use, 
> especially due to the broad possible range of integrations with other tools.

Bots are a hot topic at the moment, and the Slack team seem to be heavily 
invested in their support for a wide range of different bot integrations. Based 
on their list - which is ranked according to popularity by default - Hubot is 
the most popular. It was originally developed at Github to automate their 
company chat room and was later rewritten and made open source.

My decision to explore Hubot further was motivated by a combination of 
curiosity, a desire to learn, and the hope of adding further to the enjoyment I 
was already getting from using Slack. Hubot was immediately appealing to me 
since it is completely scriptable, and it's popularity and solid documentation 
provided further affirmation that it was worth giving a try.

### Setup process

As I alluded to above, the team at Github have provided a detailed set of 
instructions on the [docs page for hubot](https://hubot.github.com/docs/), so 
that's where you want to begin if you're looking to give this a try yourself. 
Here are the essential steps:

#### Install `node.js` and `npm` (if you don't already have these)

[Installation instructions for OS X/Windows/Linux](https://docs.npmjs.com/getting-started/installing-node)

#### Install the hubot generator using `npm`

`npm install -g yo generator-hubot`
    
#### Create a new instance of hubot

From a new directory e.g. myhubot, run:  
`yo hubot --adapter=slack`

This script will prompt you to describe the bot you are going to build, and 
create a file that NPM can use to help manage your project.

#### Setup a Custom Bot on your Slack team

Navigate to [the Custom Bot creation page](https://my.slack.com/apps/A0F7YS25R-bots) 
and click on Add Configuration. Then follow the prompts in order to register 
your bot and create a token. This token can then be used to allow your instance 
of hubot to log into your Slack team as a bot.

#### Run hubot

You can then run your instance of hubot locally using the run script below (note 
that you'll need to copy-and-paste your token in from the previous step):
 
`HUBOT_SLACK_TOKEN=xoxb-YOUR-TOKEN-HERE ./bin/hubot --adapter slack`

By default, hubot should then join your `#general` channel within Slack, where 
you can test it out e.g. with:

`<bot_name> pug me`

#### Deploy your bot

Once you’ve verified that hubot is working locally, it is a good idea to deploy 
your bot somewhere e.g. [Heroku](https://www.heroku.com/) (otherwise you'll need 
to have hubot running permanently on your local machine which probably isn't 
practical).

Again, the Github docs are great and provide setup instructions for deploying 
hubot on Heroku [here](https://hubot.github.com/docs/deploying/heroku/). They 
also have instructions for deploying on Azure, along with more general 
instructions for deploying on Unix or Windows (e.g. via another cloud computing 
service such as [Digital Ocean](https://www.digitalocean.com/)).

If you go with Heroku and are making use of their free usage tier, note that 
free dynos sleep after 30 minutes of inactivity. Since interaction is done 
through chat, hubot has to be online and in the room to respond to messages. 
To get around this, you can use the [hubot-heroku-keepalive script](https://github.com/hubot-scripts/hubot-heroku-keepalive), which can be 
configured to keep your free dyno alive (given the limitations on the free plan, 
more than 18 hours/day will require an upgrade).

#### Add your own custom scripts

So hopefully by this stage you have got your instance of hubot configured and 
deployed, so all that's left to do is to make the bot your own! For a start, 
have a look through the scripting overview provided [here](https://hubot.github.com/docs/scripting/), and then get going adding 
your own custom scripts to your bot to give it new functionality, personality 
etc.

### Hugo in action

So below are some examples of Hugo in action based on a few simple scripts I 
added after going through the above setup process.

Hugo taunts us whenever we mention [Hadley Wickham](http://hadley.nz/) (which is 
fairly often given that we're mostly working on #Rstats related stuff - such is 
the man's influence on the R community)

![Hugo taunt](/img/small-imgs/hugo_taunt.PNG "Example taunt from Hugo in response to a mention of Hadley")

He makes use of lolcats for many of his responses (because I really like lolcats)

![Hugo lolcats](/img/small-imgs/hugo_lolcats.PNG "Hugo responds/interjects with lolcats sometimes")

He celebrates when we get successful build notifications from Travis  
  
![Build Passed - lolcat](/img/small-imgs/hugo_build_passed.PNG "An example of Hugo responding to a successful build notification from Travis within the Slack app")

And occasionally the celebrations don't involve lolcats...

![Build Passed - batman](/img/small-imgs/hugo_build_passed.gif "Another example of Hugo responding to a successful build notification from Travis within the Slack app")

Strangely enough I couldn't spot any failure notifications within our Slack 
channel but when there is one, Hugo will chime in with an appropriately themed 
image - which again usually has a high chance of being a lolcat like one of 
those shown below.

![Build Failed - Shocked cat](/img/small-imgs/hugo_build_failed_2.jpg "Build failed - Shocked cat"){:style="float: left;margin-right: 15px;margin-top: 10px;margin-left: 0px;margin-bottom: 15px;"}
![Build Failed - Grumpy cat](/img/small-imgs/hugo_build_failed_3.jpg "Build failed - Grumpy cat"){:style="float: right;margin-right: 0px;margin-top: 10px;margin-left: 15px;margin-bottom: 15px;"}

All of the above examples were straightforward to script as they just rely on 
rudimentary regular expressions, but I was somewhat surprised at the 
entertainment value we got out of these simple eavesdrop and respond behaviours 
from a chat bot. Hopefully I'll be able to revisit this over time and explore 
some more advanced interactions. There are also loads of hubot scripts all over 
Github which I am yet to explore (e.g. [here](https://github.com/hubot-scripts)).

![Build Failed - Kitten](/img/small-imgs/hugo_build_failed_1.jpg "Build failed - Kitten"){:style="float: right;margin-right: 0px;margin-top: 15px;margin-left: 15px;margin-bottom: 15px;"}

If you have any ideas or suggestions for Hugo please feel free to comment below 
or take a look at [the repo](https://github.com/jonmcalder/hugobot) and submit 
a pull request - given the limited amount of time I've invested so far and the 
fact that I'm just winging it I'd love to learn from someone who actually knows 
what they're doing!