# Overview

This plugin provides a jQuery interface for inline AJAX commenting
for a blog powered by Movable Type, although a design goal is for
this plugin to be broadly applicable to any blogging platform
(e.g. WordPress, Drupal, etc). 

# Getting Started

Before you begin, make sure you have a copy of jQuery 1.3 or 
greater installed on your system/website. You will need to include 
this in your web page or Movable Type templates:

   <script type="text/javascript" src="jquery-1.3.1.js"></script>

You will also need the following jQuery plugins or libaries:

* jquery-mtgreet - http://github.com/byrnereese/jquery-mtgreet/tree
* jquery-form - bundled with Movable Type

## Add Your Script Tags

Add this code to the header of your web site.

    <script type="text/javascript" src="mt.js"></script>
    <script type="text/javascript" src="jquery.mtauth.js"></script>
    <script type="text/javascript" src="jquery.mtgreeting.js"></script>
    <script type="text/javascript" src="jquery.mtcomment.js"></script>

Note: You may need to adjust the paths to each of the scripts above
depending upon where they are located on your web server.

Note: It may not be obvious, but this also assumes you also reference
jQuery and other prerequisites. Like so:

    <script type="text/javascript" 
       src="<$mt:StaticWebPath$>jquery/jquery.js"></script>
    <script type="text/javascript" 
       src="<$mt:StaticWebPath$>jquery/jquery.form.js"></script>

## Edit Your Web Page

Now let's look at some actual HTML and javascript that you can use on 
your web site and blog:

    <html>
      <head>
        <script type="text/javascript" src="mt.js"></script>
        <script type="text/javascript" src="jquery.js"></script>
        <script type="text/javascript" src="jquery.mtauth.js"></script>
        <script type="text/javascript" src="jquery.mtgreeting.js"></script>
        <script type="text/javascript">
        $(document).ready(function(){
          if ($('#comments-form')) {
	    /* First, let's boot strap the comment form on the page. 
             * In this one step, we take the default MT comment form and turn
             * into a fully AJAXified comment form. 
             */
            var form = $('#comments-form').commentForm({ 
              insertMethod: 'append',
              target: '.comments-content'
            });
	    /* Once the form is bootstrapped and we have a handle to it
             * we then pass that as a handle to the reply function which
             * provides the inline commenting ability.
             */
            $('.reply').reply({
              sourceForm: form
            });
          };
        });
        </script>
      </head>
      <body>
        <div id="comments">
          <div class="comment">
            <p>My comment text goes here.</p>
            <p><a href="#" class="reply">Reply</a></p>
          </div>
        </div>
      </body>
    </html>

# Usage and API

## Methods

### `commentForm`

* `speed` (default: 'slow') - The speed at which animations occur.

* `entryId` (default: null) - The entry ID for which the comment is in reply. This should be embedded in the source comment form as an input element with the name `entry_id`. But if it is not, then specify this to have mtcomment forcibly set the `entry_id` form parameter upon form submit.

* `parentId` (default: 0) - The comment ID for which replies will be related. This is only relevant for inline/threaded comments.

* `leaveCommentMsg` (default: 'Leave a comment...') - This is the text that will appear in the comment box by default. The text will disappear when the user clicks inside the text area to leave a comment.

* `insertMethod` (values: "append" (default) and "after") - Determines the method jQuery will use for inserting the newly added comment into an existing thread. See jQuery's documentation on Manipulation for more info.

* `replySelector` (default: a.reply) - A selector relative to an individual comment's containing `<div>` that references the "reply" link which will open an inline comment form when clicked.

* `onSuccess` (default: null) - This is invoked when a comment has been successfully posted. The value of this option should be a function that takes the following parameters as input: form DOM element, comment DOM element, parent ID. If this is left unspecified then the plugin will simply make the comment appear. The intent of this comment is to better control the animations that govern the reveal and hiding of inline form and comment elements.

* `target` (default: '#comments-list') - The element or element selector to which new comments will be inserted according to the insertMethod specified.

### `reply`

* `speed` (default: 'slow') - The speed at which animations occur.

* `formSource` (default: '#comments-form') - The selector to the comment form source. This will serve as the template for the ajax comment form that will be automatically generated for you.

* `target` (default: '#comments-list') - The element or element selector to which new comments will be inserted.

# License and Copyright

Copyright 2009, Open Melody Software Group. All rights reserved.

This plugin is licensed under the same terms as jQuery itself. 
