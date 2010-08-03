A patch to the Drupal image module

I wrote a small patch to the [Drupal][1] [image module][2] that
provides a block to list the top-level image taxonomy terms (the set
of galleries you get when you browse to `.../image`):

[1]: http://www.drupal.org/
[2]: http://www.drupal.org/projects/image

    Index: modules/image/image.module
     ===================================================================
    RCS file: /cvs/drupal-contrib/contributions/modules/image/image.module,v
    retrieving revision 1.107
    diff -u -r1.107 image.module
    --- modules/image/image.module	7 Mar 2004 12:52:19 -0000	1.107
    +++ modules/image/image.module	10 Apr 2004 01:43:02 -0000
    @@ -66,6 +66,22 @@
     }
     
     /**
    + * Image block
    + */
    +function image_block($op = "list", $delta = 0) {
    +    // listing of blocks, such as on the admin/system/block page
    +    if ($op == "list") {
    +	$blocks[0]["info"] = t("Image Gallery");
    +    } else {
    +	// our block content
    +	$blocks["subject"] = t(variable_get("image_block_title", "Image Galleries"));
    +	$albums = taxonomy_get_children($tid, variable_get("image_nav_vocabulary", ""));
    +	$blocks["content"] = theme_image_block($albums);
    +    }
    +    return $blocks;
    +}
    +
    +/**
      * Node descriptor
      */
     function image_node_name() {
    @@ -152,6 +168,8 @@
     
       $output .= form_select(t("Disable Image Caching"), "image_random_suffix",  \
    	variable_get("image_random_suffix", "0"), array("0" => "disabled",    \
    	"1" => "enabled"), t("Enabling this will add random parameters "      \
    	. "to image URIs which will prevent the browser from caching."));
     
    +  $output .= form_textfield(t("Title for image block"), "image_block_title", \
            variable_get("image_block_title", "Image Galleries"), 20, 80,         \
    	t("The title to display in the image block, if enabled"));
    +
       return $output;
     }
     
    @@ -1401,6 +1419,18 @@
       return '<hr>' . theme("pager", $tags, $limit, $element, $attributes);
     }
     
    +/**
    + * Theme function to render the image block contents
    + */
    +function theme_image_block($albums) {
    +    $output = "<div class=\"menu\"><ul>";
    +    foreach ($albums as $album) {
    +	$output .= "<li class=\"collapsed\">" . l($album->name, "image/tid/"  \
    		   . $album->tid) . "</li>";
    +    }
    +    $output .= "</ul></div>";
    +    return $output;
    +}
    +
     /****
     ***** admin function and friends
     ****/
 
A nice enhancement would be to allow control over the depth of
taxonomy terms to include (that is, display child galleries in the
block, to a user-defined level).

