---
layout: post
title: Displaying a node in a block
---

I had a requirement to display a custom block on a drupal site, but to
allow site editors to update the contents of the block. I don't
particularly want to give editors the ability to create/edit blocks,
but they are already able to edit nodes.

A bit of PHP in a custom block does the (somewhat hackish) trick:

    <?php
    $node = node_load(array('nid'=>1));
    echo node_view($node, false, false, false);
    ?>

Obviously, the node id is hard coded. No, this is not elegant or
ideal, but it quickly solves a problem.

Also worth noting is the following snippets of template.php and
node.tpl.php in my phptempalte theme, which allow me to contol the
display of the "info" (attribution) for node types--if you use a page
node as the target of the above block, you can turn off the display of
the info line while not affecting story nodes.

template.php:

    <?
    function _phptemplate_variables($hook, $vars) {
    	switch ($hook) {
    	case 'node':
    		$vars['info'] = theme_get_setting('toggle_node_info_' . $vars['node']->type);
    		break;
    	}
    	return $vars;
    }
    ?>

node.tpl.php:

    <?php if ($info): ?>
      <div class="info"><?php print $submitted ?>
      <?php if ($terms): ?>[<?php print $terms ?>]<?php endif; ?>
      </div>
    <?php endif; ?>

