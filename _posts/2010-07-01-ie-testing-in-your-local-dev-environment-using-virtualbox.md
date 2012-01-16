--- 
layout: post
title: "IE testing in your local dev environment using VirtualBox "
created: 1277975307
---
I do my web development on my laptop and set up each site as a virtual hosts (e.g. *site1.localhost*, *site2.localhost*). In every project you eventually need to do the dreaded IE testing, but how do you access these sites from a virtualized Windows running in VirtualBox?
<!--break-->
The (physical) *host* (in VirtualBox terminology) machine can be accessed from (virtual) *guests* using the IP 10.0.2.2. To access individual virtual hosts on the host machine you need to use the same host name on the Windows box. 

In Windows, fire up Notepad as Administrator and edit the hosts file located at: `%windir%\system32\drivers\etc\hosts`.

We add a line with our virtual hosts and save it:

    10.0.2.2 	site1.localhost site2.localhost

You should now be able to access site1.localhost and site2.localhost from Windows.

I've set up a convenient shortcut on the Windows desktop for editing the hosts file. Here's how you do it:

1. Create a shortcut to Notepad on your desktop and change its name to "hosts". 
2. Right-click it, select Properties. 
3. Go to the Shortcut tab and change the Target path to:
`%windir%\system32\notepad.exe %windir%\system32\drivers\etc\hosts`
4. Click Advanced and check "Run as Administrator". Click OK in both dialogs to save your settings. 

Your hosts file is now just a double-click away.
