--- 
layout: post
title: "Joomla has a security problem: The benefits of centralized contrib hosting"
created: 1275389174
---
I'm helping a client do a security audit of one of their servers. A couple of their sites are running Joomla, which I have very little experience with, so as part of this I set out to compile a list of available exploits to compare against their Joomla versions and installed components. What I discovered was frankly stunning.

<table>
  <tr>
    <th>Platform</th><th>Number of exploits</th> 
  </tr>
  <tr>
    <td>Drupal</td><td><a href="http://www.exploit-db.com/list.php?description=drupal">9</a></td>
  </tr>
  <tr>
    <td>Typo3</td><td><a href="http://www.exploit-db.com/list.php?description=typo3">2</a></td>
  </tr>
  <tr>
    <td>Wordpress</td><td><a href="http://www.exploit-db.com/list.php?description=wordpress">70</a></td>
  </tr>
  <tr>
    <td>Joomla</td><td><a href="http://www.exploit-db.com/list.php?description=joomla">637</a></td>
  </tr>
</table>

Source: [The Exploit Database](http://www.exploit-db.com/) 

It's important to emphasize that just one critical hole in your site is enough, so running Drupal or Typo3 might still get you hacked. However, it's clear that the Joomla community has a very serious security problem. Judging from the number of SQL injection exploits on the list it seems like it might be a question of educating contributors. 

I believe that Drupal's low number is a strong testament to the benefits of hosting contributed code on drupal.org. This approach means that:

- You need to apply for a CVS account and as part of that your first contributed code [will get vetted](http://drupal.org/node/539608) by someone experienced.
- Hosted code is audited by the [drupal.org Security Team](http://drupal.org/node/32750).

A low bar of entry for new contributors is great, but can be dangerous without a strong security support structure.

Note: A [Swedish version](http://www.searchfactory.se/blogg/2010/6/sakerhetsfordelarna-med-centraliserad-kod) of this entry is available over at the [Searchfactory](http://www.searchfactory.se) blog.
