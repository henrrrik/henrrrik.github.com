---
layout: post
title: "Asset pipeline woes on Heroku"
created: 1332438712
---

I ran into a confusing issue on Heroku where Devise/ActiveAdmin makes a
DB call during the precompile process. On Heroku the DB isn't available
at that point, causing the precompilation to fail.

{% highlight console %}
-----> Preparing app for Rails asset pipeline
       Running: rake assets:precompile
       rake aborted!
       could not connect to server: Connection refused
       Is the server running on host "127.0.0.1" and accepting
       TCP/IP connections on port 5432?
{% endhighlight %}

You can work around this in two ways. Either precompile locally and
deploy the precompiled assets, or turn on the labs feature in Heroku. 
This makes the DB available during the precompilation, and also let's
you use a more recent ruby. 1.9.3-p125 was the latest one available as
I'm writing this.

{% highlight console %}
$ heroku plugins:install https://github.com/heroku/heroku-labs.git
$ heroku labs:enable user_env_compile -a myapp
$ heroku config:add RUBY_VERSION=ruby-1.9.3-p125
{% endhighlight %}

