--- 
layout: post
title: Setting up a Drupal development environment with lighttpd in Mac OS X
created: 1197678252
---
After upgrading to Leopard I finally got fed up with MAMP. I've been using it for development for quite a while, but found it dog slow and I never got the GUI tools to work right under 10.5. I've been using [lighttpd](http://www.lighttpd.net) on servers and decided to give it a go on my MacBook Pro. It runs superbly.
 
This setup requires MacPorts which is a Linux style package management system. Everything you install through MacPorts is contained in the folder `/opt` and it doesn't tamper with your existing OS X installation.
<!--break-->  
##Install MacPorts
Download and install [MacPorts](http://www.macports.org/). Note that MacPorts requires that Apple's [Xcode Developer Tools](http://developer.apple.com/tools/xcode/) package is installed.

You can verify that you're using the latest version of MacPorts by firing up the Terminal and running:

    sudo port selfupdate

<strong>Note:</strong> If you don't already have a `~/.profile` file, you may need to create it and add the paths for MacPorts (close and re-open your Terminal window for the changes to become active). Edit `~/.profile` and insert:

    export PATH=/opt/local/bin:/opt/local/sbin:$PATH

##Install and configure lighttpd and PHP
We're using the CML version of lighttpd for URL rewriting so we need Lua too. Run:

    sudo port install lua && sudo port install lighttpd +cml

Since it compiles the packages from source this takes a while.

###Setting up lighttpd.conf
Make a copy of the config template that lighttpd comes with:

    cd  /opt/local/etc/lighttpd/
    sudo cp lighttpd.conf.default lighttpd.conf


Edit `lighttpd.conf` and enable `mod_magnet` and `mod_fastcgi` in the `server.modules` section.

Enable PHP with FastCGI by adding this snippet somewhere below the `server.modules` section:

    fastcgi.server = (
        ".php" => ((
            "bin-path" => "/opt/local/bin/php-cgi",
            "socket" => "/opt/local/var/run/php5/php.socket"
        ))
    )

Set the primary site directory. I like to use my Sites folder:

    server.document-root        = "/Users/yourname/Sites/"

Add your first virtual host:

    $HTTP["host"] == "mydrupalsite" {
      server.document-root = "/Users/yourname/Sites/mydrupalsite/"
      index-file.names = ( "index.php" )
      magnet.attract-physical-path-to = ( "/opt/local/etc/lighttpd/drupal.lua" )
    }

###Rewrite rules
lighttpd doesn't support Apache's esoteric mod_rewrite syntax, so the `.htaccess` file that Drupal ships with doesn't do us any good. Instead we'll use a Lua script to handle clean URLs.

Create the file `/opt/local/etc/lighttpd/drupal.lua` containg the following code:

    attr = lighty.stat(lighty.env["physical.path"])
    if (not attr) then
    lighty.env["uri.query"] = "q=" .. string.gsub(lighty.env["request.uri"], "?", "&")
    lighty.env["uri.path"] = "/index.php"
    lighty.env["physical.rel-path"] = lighty.env["uri.path"]
    lighty.env["physical.path"] = lighty.env["physical.doc-root"] .. lighty.env["physical.rel-path"]
    end

This a slightly updated version of <a href="http://pixel.global-banlist.de/2007/2/6/drupal-on-lighttpd-with-clean-urls">darix's script</a>. The original version couldn't pass on GET variables appended to the clean URL (e.g. for exposed Views filters where the URL ends up looking something like: `/somepage?filter0=x&filter1=y`).

###Add the new hostname

We need to add the hostname to `/etc/hosts`:

    127.0.0.1       mydrupalsite

<strong>Note:</strong> In the version I downloaded lighttpd's default log directory was set to `/var/log/lighttpd`, but since that is outside of `/opt` it isn't created. lighttpd <strong>will not start</strong> if it can't write to that directory, so I created it:

    sudo mkdir /var/log/lighttpd

Make lighttpd start at boot up:

    sudo launchctl load -w /Library/LaunchDaemons/org.macports.lighttpd.plist

Start lighttpd:

    sudo launchctl start org.macports.lighttpd

<strong>Troubleshooting tip:</strong> If lighttpd fails to start (for example if you have a typo in your lighttpd.conf), you can get a more verbose output by running:

    sudo lighttpd -D -f /opt/local/etc/lighttpd/lighttpd.conf

##Installing MySQL

    sudo port install mysql5 +server

Install the MySQL system tables:

    sudo mysql_install_db5 --user=mysql

Make it run at startup:

    sudo launchctl load -w /Library/LaunchDaemons/org.macports.mysql5.plist

Fire it up:

    sudo launchctl start org.macports.mysql5

Now you can log in and secure the initial user accounts (as described [here](http://dev.mysql.com/doc/refman/5.0/en/default-privileges.html)), and create your Drupal database:

    mysql5 -u root


##Install PHP

PHP 5 is pre-installed in Leopard, but the Apple built version lacks GD support and we need the FastCGI version too.

    sudo port install php5 +fastcgi +mysql5

Create the socket directory for php-cgi (as defined in the `fastcgi.server` section of `lighttpd.conf` above):

    sudo mkdir -p /opt/local/var/run/php5


##Install Drupal
Install Drupal in `/Users/yourname/Sites/mydrupalsite/`.
Now open up your browser and go to http://mydrupalsite/. Voila!

To add another virtual server, just duplicate the `$HTTP["host"]` entry in `lighttpd.conf` and give it the new hostname and path. Don't forget to add the new hostname to `/etc/hosts`. Easy as pie.

##Install phpMyAdmin
phpMyAdmin is nice to have.

    sudo port install phpmyadmin

Edit `/opt/local/etc/lighttpd.conf` and add it as a virtual host (no need for the rewrite rules):

    $HTTP["host"] == "phpmyadmin" {
      server.document-root = "/opt/local/www/phpmyadmin/"
    }

Add the hostname to `/etc/hosts`:

    127.0.0.1       phpmyadmin

Make a copy of the template configuration file:

    sudo cp /opt/local/www/phpmyadmin/config.sample.inc.php /opt/local/www/phpmyadmin/config.inc.php

Edit `/opt/local/www/phpmyadmin/config.inc.php` and enter something as the `$cfg['blowfish_secret']`.

Restart lighttpd:

    sudo launchctl stop org.macports.lighttpd
    sudo launchctl start org.macports.lighttpd

Point your browser to [http://phpmyadmin/](http://phpmyadmin/).


##Create an Apple Script for starting and stopping lighttpd and MySQL
I created a simple Apple Script that restarts lighttpd and MySQL. Fire up Script Editor and paste this in there:

    do shell script "sudo launchctl stop org.macports.lighttpd && sudo launchctl start org.macports.lighttpd && sudo launchctl stop org.macports.mysql5 && sudo launchctl start org.macports.mysql5" with administrator privileges

Save it as an application and add it to your dock. Now you can restart the server without having to do any typing in the Terminal.

