--- 
layout: post
title: Serving the same H.264 video file to everyone, from Internet Explorer to iPhone
created: 1255617068
---
In a comment on my previous post [Using the HTML5 &lt;video&gt; tag with a Flash fallback](http://henriksjokvist.net/archive/2009/2/using-the-html5-video-tag-with-a-flash-fallback), Rob Colburn suggested serving everyone the same H.264 video file.
<!--break-->
The big upside is that we only need to convert the video into one format. The downside is that we lose legacy Flash compatibility and Firefox 3.5+ and Opera 10+ users get Flash instead of HTML5 video.

This can be accomplished the following JavaScript code. Note that also I've replaced JWPlayer with [Flashy](http://code.google.com/p/flashy/), a GPL-licensed alternative.

{% highlight html %}
<script type="text/javascript">
  $(document).ready(function() {
    var v = document.createElement("video"); // Are we dealing with a browser that supports <video>? 
    if ( !v.play || $.browser.mozilla || $.browser.opera) { // If no, or Firefox or Opera, use Flash.
      var params = {
        allowFullScreen: "true"
      };
      var flashvars = {
        video: "video.m4v",
        poster: "snapshot.jpg",
        maintainAspectRatio: "false"
      };
      swfobject.embedSWF("videoPlayer.swf", "demo-video-flash", "480", "272", "9.0.0", "expressInstall.swf", flashvars, params);
    }
  });
</script>
{% endhighlight %}

Here's the [example in action](http://henriksjokvist.net/examples/html5-video-v2/).
