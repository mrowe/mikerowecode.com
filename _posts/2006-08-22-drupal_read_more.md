---
layout: post
title: An inline "read more" link
---

Summary pages in [Drupal][1], such as [this one][2], display "teasers"
of the nodes (pages or blog entries) if the entry is longer than a
certain (configurable) length. If the entry is trimmed this way,
Drupal helpfully displays a "read more" link below the teaser. This is
ok, but it can be easy to miss if you are just looking at the text of
the article, and mentally "tuning out" the noise of the links below.

[1]: http://drupal.org/
[2]: /blogs/michael

So I modified my theme's `node.tpl.php` (part of the now-default
[PHPTemplate][3] engine) to hack in a "more" link at the end of the
last paragraph:

[3]: http://drupal.org/phptemplate

    --- node.tpl.php        (revision 43)
    +++ node.tpl.php        (working copy)
    @@ -9,6 +9,11 @@
                <span class="taxonomy">[<?php print $terms?>]</span>
              <?php }; ?>
            <?php }; ?>
    +    <?php if ($readmore) {
    +        // insert a link before the closing P tag if there's more to read
    +       $read_more = ' <span class="readmore">...<a href="' . $node_url . '">more</a>...</span>';
    +       $content = preg_replace('/<\/p>$/', $read_more . '$1', $content); 
    +    }; ?>
         <div class="content"><?php print $content?></div>
         <?php if ($links) { ?><div class="links">&raquo; <?php print $links?></div><?php }; ?>
       </div>

This requires a variable, `$readmore`, to be set in `template.php`:

    function _phptemplate_variables($hook, $vars) {
    	switch ($hook) {
    	case 'node':
    		$vars['readmore'] = ($vars['node']->teaser && $vars['node']->readmore);
    		break;
    	}
    	return $vars;
    }
    
(and some styling in `style.css`, but you can figure this out for
yourself ;) )

And now I have discrete little "...more..." links at the end of the
last paragraph of trimmed article summaries.

I kind of feel like this should have been possible by simply adding
the link after `$content` rather than stuffing something into it with
a regex, and causing it to be displayed inline with CSS, but my CSSfu
is not strong enough. Suggestions welcome...
