---
layout: post
title: "Module Puppy - Giving Hubot a drop of Drupal knowledge"
created: 1328005417
---
We've been using the delightful [Hubot](http://hubot.github.com) chat bot with Campfire for a while now and I've been looking to ways to add Drupal-related features to it.

One idea was to add a `module me <query>` command that searches for modules. Unfortunately drupal.org doesn't offer an API, but the update module uses a [humongous XML file](http://updates.drupal.org/release-history/project-list/all) that contains some useful information like name, short name, creator and url for all projects hosted on drupal.org.

I wrote a little [Sinatra](http://www.sinatrarb.com/) app called [Module Puppy](http://modulepuppy.heroku.com) that pulls down and parses relevant data from this XML file and makes it available via a simple JSON API. The app is hosted on [Heroku](http://www.heroku.com/) which uses PostgreSQL as the default database. This meant that I could use the very nice multi-column search feature in PostgreSQL (easy with the [texticle](https://github.com/tenderlove/texticle) gem).

The Hubot talks to the Sinatra app using [a small CoffeeScript plugin](https://github.com/github/hubot-scripts/blob/master/src/scripts/modulepuppy.coffee),
part of the [hubot-scripts](https://github.com/github/hubot-scripts) collection 
This makes Hubot listen for the phrase:

    there's a module for <something>

and also, the more direct command:

    <bot name> module me <something>

In practice, it looks like this:

<img src="/images/hubot-modulepuppy.png" alt="" />



