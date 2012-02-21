---
layout: post
title: "Deploying with rbenv and Capistrano"
created: 1329765756
---

I've recently decided to move away from [RVM](https://rvm.beginrescueend.com/) in favor of [rbenv](https://github.com/sstephenson/rbenv). I thought RVM was a bit too finicky to use in production and I wanted something simpler that I could wrap my head around.

This post is more or less an attempt to collect what I figured out from reading [an issue thread on GitHub](https://github.com/sstephenson/rbenv/issues/101), George Ornbo's [great post about rbenv](http://shapeshed.com/using-rbenv-to-manage-rubies/) and the [rbenv wiki page on deployment](https://github.com/sstephenson/rbenv/wiki/Using-rbenv-in-production#wiki-method1).

## On the server

As the deployment user (in my case `deploy`), run the `rbenv-installer`:

    $ curl -L https://raw.github.com/fesplugas/rbenv-installer/master/bin/rbenv-installer | bash

Add rbenv to your PATH. The second command adds shims and autocompletion:

    $ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.profile
    $ echo 'eval "$(rbenv init -)"' >> ~/.profile

Reload the profile, install one or more rubies and then rehash to refresh
the shims:

    $ source ~/.profile
    $ rbenv install 1.9.3-p125
    $ rbenv rehash

Set your new rbenv ruby as the new system-wide default ruby for this user:

    $ rbenv global 1.9.3-p125

Now it's time to go ahead and install your Passenger, Unicorn, or what have you.


## Capistrano

Now, let's turn our attention to Capistrano and our `deploy.rb`.

We want Bundler to handle our gems and we want it to package everything
locally with the app. The `--binstubs` flag means any gem executables will be added
to `<app>/bin` and the `--shebang ruby-local-exec` option makes sure we'll use the
ruby version defined in the `.rbenv-version` in the app root. Note that he
`--shebang` flag requires Bundler 1.1.

{% highlight ruby %}
require "bundler/capistrano"
set :bundle_flags, "--deployment --quiet --binstubs --shebang ruby-local-exec"
{% endhighlight %}

If you don't want to use a globally installed Bundler, you need to add a
binstub for it manually and tell Capistrano to use that:

{% highlight ruby %}
set (:bundle_cmd) { "#{release_path}/bin/bundle" }
{% endhighlight %}

The binstub itself (`<app>/bin/bundle`) should look like this:

{% highlight ruby %}
#!/usr/bin/env ruby-local-exec

require 'rubygems'
version = ">= 0"

if ARGV.first =~ /^_(.*)_$/ and Gem::Version.correct? $1 then
  version = $1
  ARGV.shift
end

gem 'bundler', version
load Gem.bin_path('bundler', 'bundle', version)
{% endhighlight %}

Since the `.profile` `PATH` settings we added earlier won't get run by Capistrano we need to add the rbenv paths to our `deploy.rb`. 
Note that we add the `shims` folder directly here instead of running `rbenv init -`. It's easier and autocomplete is wasted on Capistrano.

{% highlight ruby %}
set :default_environment, {
  'PATH' => "$HOME/.rbenv/shims:$HOME/.rbenv/bin:$PATH"
}
{% endhighlight %}

That's pretty much it.
