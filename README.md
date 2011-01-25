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

## Comment Response Template

In order to enable AJAX commenting on your blog, you need to do the following:

1) Set the comments preference labeled "Enable comment response template" to on. It is located under Tools > Blog Settings.

2) Replace the contents of the Comment Response system template with:

      <mt:Include module="CN: Global Variables">
      <mt:SetVarBlock name="comment_new"><mt:CommentID></mt:SetVarBlock>
      <mt:If name="request.ajax">
        <mt:If name="comment_pending">
          <mt:Ignore><!-- Pending message when comment is being held for review --></mt:Ignore>
          <$mt:Var name="message" value="Your comment has been received and held for approval by the blog owner."$>
        <mt:Else name="comment_error">
          <mt:Ignore><!-- Error message when comment submission fails --></mt:Ignore>
          <mt:SetVarBlock name="message">Your comment submission failed for the following reasons:
          <$mt:ErrorMessage$></mt:SetVarBlock>
        <mt:Else>
          <mt:If name="request.json">
            <mt:Include module="Individual Comment">
          <mt:else>
            <mt:Include module="Individual Comment" use_html="1">
         </mt:If>
        </mt:If>
        <mt:If name="message">
          <mt:If name="request.json">
      {
      "message": "<$mt:var name="message" encode_json="1"$>"
      }
          <mt:Else>
            <div id="comment-error" class="message"><p><$mt:var name="message"$></p></div></mt:If></mt:If>
      <mt:Else>
      <html>
        <head>
          <meta http-equiv="refresh" content="0;URL=<mt:EntryPermalink escape="html">?r=<mt:Date format="%S%H%M">">
        </head>
      </html>
      </mt:If>

3) Create a template module called "Individual Comment." Its contents should be:

      <mt:section trim="1">
      <mt:SetVarBlock name="current_comment"><$mt:CommentID$></mt:SetVarBlock>
      <mt:setvarblock name="comment_html">
        <div comment_id="<$mt:CommentID$>" id="comment-<$mt:CommentID$>" class="pkg comment<mt:if name="comment_new" eq="$current_comment"> comment-highlight</mt:if><mt:IfCommentParent> reply</mt:IfCommentParent><mt:IfCommenterIsEntryAuthor> by-author</mt:IfCommenterIsEntryAuthor> depth-<$mt:var name="depth"$>">
        <mt:setvarblock name="commenterlink"><$mt:CommentAuthorLink$></mt:setvarblock>
          <p class="author">
            <mt:IfCommentParent>
              <span class="vcard"><$mt:var name="commenterlink"$></span> said:</a>
            <mt:else>
              <span class="vcard"><$mt:var name="commenterlink"$> said:</span>
            </mt:IfCommentParent>
          </p>
          <div class="comment-content pkg">
            <div class="comment-text"><$MTCommentBody$></div>
            <div class="comment-footer">
              <div class="date">Posted on <a href="<$mt:CommentLink$>"><abbr class="published" title="<$MTCommentDate format_name="iso8601"$>"><$MTCommentDate$></abbr></a></div>
              <div class="actions pkg">
                <MTIfCommentsAccepted>
                <a href="javascript:void(0);" class="reply" title="Reply">Reply</a>
                </MTIfCommentsAccepted>
              </div>
            </div>
          </div>
        </div>
      </mt:setvarblock>
      </mt:section><mt:if name="use_html">
        <$mt:var name="comment_html"$>
      <mt:else>{
        "id":            "<$mt:CommentID encode_json="1"$>",
        "date":          "<$mt:CommentDate encode_json="1"$>",
        "text":          "<$mt:CommentBody remove_html="1" encode_json="1"$>",
        "comment_count": "<mt:CommentEntry><$mt:EntryCommentCount singular="1 Comment" plural="# Comments" none="No Comments" encode_json="1"$></mt:CommentEntry>",
        "commenter":     "<$mt:CommenterName encode_json="1"$>",
        "commenter_url": "<$mt:CommentAuthorLink encode_json="1"$>",
        "parent":        "<mt:IfCommentParent><mt:CommentParent><$mt:CommentID$></mt:CommentParent><mt:else>0</mt:IfCommentParent>",
        "html":          "<$mt:var name="comment_html" encode_json="1"$>"
      }
      </mt:if>

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
