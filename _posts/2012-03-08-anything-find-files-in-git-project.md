---
layout: post
title: Emacs Anything - Find files in a git project
---

If you use [Emacs][] you really should take a look at [Anything][]. When you do, you'll probably want to use it to replicate TextMate's fabled "Go to file...". Ken Wu wrote [a nice little anything-source][1] that uses git to derive a file list for a project, but he was obviously using an old version of [magit][]. Here's a tweaked version of his code that works with Magit v1.1.1:

{% highlight cl %}
(defvar anything-c-source-git-project-files-cache nil "(path signature cached-buffer)")
(defvar anything-c-source-git-project-files
  '((name . "Files from Current GIT Project")
    (init . (lambda ()
              (let* ((top-dir (file-truename (magit-get-top-dir (if (buffer-file-name)
                                                                    (file-name-directory (buffer-file-name))
                                                                  default-directory))))
                     (default-directory top-dir)
                     (signature (magit-rev-parse "HEAD")))

                (unless (and anything-c-source-git-project-files-cache
                             (third anything-c-source-git-project-files-cache)
                             (equal (first anything-c-source-git-project-files-cache) top-dir)
                             (equal (second anything-c-source-git-project-files-cache) signature))
                  (if (third anything-c-source-git-project-files-cache)
                      (kill-buffer (third anything-c-source-git-project-files-cache)))
                  (setq anything-c-source-git-project-files-cache
                        (list top-dir
                              signature
                              (anything-candidate-buffer 'global)))
                  (with-current-buffer (third anything-c-source-git-project-files-cache)
                    (dolist (filename (mapcar (lambda (file) (concat default-directory file))
                                              (magit-git-lines "ls-files")))
                      (insert filename)
                      (newline))))
                (anything-candidate-buffer (third anything-c-source-git-project-files-cache)))))

    (type . file)
    (candidates-in-buffer)))
{% endhighlight %}

I tried to update the [Emacs Wiki][] page to include this fix but couldn't. Not sure what I was doing wrong... The changes I made to Ken's code:

{% highlight diff %}
@@ -6,7 +6,7 @@
                                                                     (file-name-directory (buffer-file-name))
                                                                   default-directory))))
                      (default-directory top-dir)
-                     (signature (magit-shell (magit-format-git-command "rev-parse --verify HEAD" nil))))
+                     (signature (magit-rev-parse "HEAD")))
 
                 (unless (and anything-c-source-git-project-files-cache
                              (third anything-c-source-git-project-files-cache)
@@ -20,10 +20,14 @@
                               (anything-candidate-buffer 'global)))
                   (with-current-buffer (third anything-c-source-git-project-files-cache)
                     (dolist (filename (mapcar (lambda (file) (concat default-directory file))
-                                              (magit-shell-lines (magit-format-git-command "ls-files" nil))))
+                                              (magit-git-lines "ls-files")))
                       (insert filename)
                       (newline))))
                 (anything-candidate-buffer (third anything-c-source-git-project-files-cache)))))
{% endhighlight %}

[Emacs]: http://www.gnu.org/software/emacs/
[Anything]: http://www.emacswiki.org/emacs/Anything
[Emacs Wiki]: http://www.emacswiki.org/
[magit]: http://magit.github.com/magit/

[1]:http://www.emacswiki.org/emacs/AnythingSources#toc64
