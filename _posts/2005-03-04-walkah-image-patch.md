A patch to walkah's image module

I just sent James the following patch to his new revamped <a
href="http://drupal.org/">drupal</a> <a
href="http://cvs.drupal.org/viewcvs/drupal/contributions/sandbox/walkah/image/">image
module</a>. It allows an admin to configure the title of the image
modules "latest" and "random" image blocks.

<pre>
Index: sandbox/walkah/image/image.module
===================================================================
RCS file: /cvs/drupal-contrib/contributions/sandbox/walkah/image/image.module,v
retrieving revision 1.16
diff -u -r1.16 image.module
--- sandbox/walkah/image/image.module   3 Mar 2005 15:44:48 -0000       1.16
+++ sandbox/walkah/image/image.module   4 Mar 2005 11:38:37 -0000
@@ -79,6 +79,11 @@
   $size_group .= form_checkbox(t('Allow users to view original image'), 
                           'image_view_original', 1, variable_get('image_view_original', 0));
   $output.= form_group(t('Image sizes'), $size_group);
 
+  // block titles
+  $titles .= form_textfield(t('Latest Image block title'), 
                   'image_block_title_latest', 
                   variable_get('image_block_title_latest', 'Latest image'), 30, 255);
+  $titles .= form_textfield(t('Random Image block title'), 
                   'image_block_title_random', 
                   variable_get('image_block_title_random', 
                   'Random image'), 30, 255);
+  $output.= form_group(t('Block Titles'), $titles);
+
   return $output;
 }
 
@@ -212,12 +217,12 @@
       switch($delta) {
         case 0:
           $images = image_get_latest();
-          $block['subject'] = t('Latest image');
+          $block['subject'] = variable_get('image_block_title_latest', 'Latest image');
           $block['content'] = l(image_display($images[0], 'thumbnail'), 'node/'.$images[0]->nid);
           break;
         case 1:
           $images = image_get_random();
-          $block['subject'] = t('Random image');
+          $block['subject'] = variable_get('image_block_title_random', 
                                          'Random image');
           $block['content'] = l(image_display($images[0], 'thumbnail'),
                                          'node/'.$images[0]->nid);
           break;
       }
</pre>
