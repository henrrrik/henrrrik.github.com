---
layout: post
title: Using the HTML5 &lt;video&gt; tag with a Flash fallback
created: 1235696691
---
The HTML5 `<video>` tag makes it possible to embed video clips in web pages much like how the `<img>` tag works for images. The browser itself provides the playback functionality without any need for plugins like Quicktime or Flash.
<!--break-->
<ins>Update: Firefox 3.5+ doesn't like the .ogm extension for the Ogg Theora video file, I've changed it to .ogg.</ins>

In it's simplest form, the tag can look like this:
{% highlight html %}
<video src="someclip.mp4" controls />
{% endhighlight %}
Safari (version 3.1 onwards) and Firefox 3.1b2 support `<video>`. Opera has released [experimental builds](http://dev.opera.com/articles/view/a-call-for-video-on-the-web-opera-vid/) demonstrating preliminary support.

Safari plays back MPEG4 (and at least on the Mac, anything that QuickTime can play). Firefox currently only supports Ogg Theora.

This lack of (default) codec support overlap can be overcome by providing both formats in the same `<video>` tag:

{% highlight html %}
<video controls>
 <source src="video.m4v" type="video/mp4" /> <!-- MPEG4 for Safari -->
 <source src="video.ogg" type="video/ogg" /> <!-- Ogg Theora for Firefox 3.1b2 -->
</video>
{% endhighlight %}

For legacy browsers that don't support `<video>` we need to use an alternative. An easy solution is to degrade to Flash using [SWFObject](http://code.google.com/p/swfobject/).

Here's the HTML:

{% highlight html %}
<div id="demo-video-flash">
  <video id="demo-video" poster="snapshot.jpg" controls>
    <source src="video.m4v" type="video/mp4" /> <!-- MPEG4 for Safari -->
    <source src="video.ogg" type="video/ogg" /> <!-- Ogg Theora for Firefox 3.1b2 -->
  </video>
</div>
{% endhighlight %}

We place the `<video>` tag inside the `<div>` that we use to attach the SWFObject object.

Now for some JavaScript:

{% highlight html %}
<script type="text/javascript">
  $(document).ready(function() { // ... a dash of jQuery.
    var v = document.createElement("video"); // Are we dealing with a browser that supports <video>?
    if ( !v.play ) { // If no, use Flash.
      var params = {
        allowfullscreen: "true",
        allowscriptaccess: "always"
      };
      var flashvars = {
        file: "video.f4v",
        image: "snapshot.jpg"
      };
      swfobject.embedSWF("player.swf", "demo-video-flash", "480", "272", "9.0.0", "expressInstall.swf", flashvars, params);
    }
  });
</script>
{% endhighlight %}

The browser support detection is done by creating a video element and attempting to play it. If this fails, the Flash player is inserted using SWFObject.

Here's the [example in action](/examples/html5-video/).

A downside to this approach as opposed to using Flash or another plugin is obviously that we currently need to provide three versions of every clip we want to embed (MPEG4, Ogg and FLV).

Hopefully the browser makers will have settled on a baseline codec by the time the final versions of Firefox 3.1 and Opera 10 (or whatever version finally brings the support) are released.
