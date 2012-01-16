--- 
layout: post
title: Duplicate id attributes for submit buttons in Drupal 5.x
created: 1217523790
---
Drupal 5 doesn't generate unique id attributes for form submit buttons. This means that if there's both a login form and a search box on the same page they will both use the same `edit-submit` id causing the page to fail validation.
<!--break-->

This has been [fixed in Drupal 6](http://drupal.org/node/111719). You can solve it in Drupal 5 by inserting the following into `template.php`:

    <?php
    /**
    * Quick fix for the validation error: 'ID "edit-submit" already defined' or edit-name
    * There is a solution in d6 core: http://drupal.org/node/111719
    */
    function phptemplate_submit($element) {
       static $count_double_id=0;
    
      $tmp = str_replace('edit-submit', 'edit-submit-'. $count_double_id++, theme('button', $element));
      return str_replace('edit-name', 'edit-name-'. $count_double_id++, $tmp);
    }
    ?>
(Snippet by [stevenQ](http://drupal.org/node/111719#comment-712049))

