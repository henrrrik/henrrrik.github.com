--- 
layout: post
title: Spruce up your Drupal search box for Safari users (Updated for Drupal 6)
created: 1220001737
---
My favourite browser Safari has support for a snazzy custom input field type called "search". This gives the field a rounded look and the ability to remember search history (amongst other things). The major drawback is of course that the HTML will no longer validate since this feature isn't part of the official spec. 

In order get around this problem I whipped up a jQuery snippet that dynamically changes the Drupal search input field to a Safari search field if the visitor uses Safari. You can see it in action in the right sidebar on this site.

This goes into `template.php`:

    drupal_add_js(path_to_theme() .'/fancysearch.js', 'theme');

This goes into `fancysearch.js` in your theme folder:

    Drupal.behaviors.fancySearch = function(context) {
      if (jQuery.browser.safari) {
        // jQuery 1.2.x doesn't support type alteration.
        var input = document.getElementById('edit-search-block-form-1');
        input.type = 'search';
        $('#edit-search-block-form-1').attr({ 
                  autosave: 'net.henriksjokvist.search',
                  results: '9',
                });
        $('#block-search-0 :submit').hide();
      }
    };
    
You will probably need to change the ids `#block-search-0` (the form's containing `div`) and `#edit-search-block-form-1` (the id of the `input` field) to whatever ids your theme uses.
`autosave` is the name under which the search history is stored and should be unique for your site, `results` is the number of past searches to remember and `placeholder` is the placeholder text inside the field.

