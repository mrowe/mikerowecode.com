---
layout: post
title: Find files in a git project redux
date: "2012-08-02"
url: "/2012/08/anything-find-files-not-in-git.html"
---

In my [previous post][] I described an update to an Emacs Anything source to "Find files in a git project". This works great if you are inside an git-managed project, but fails horribly if you are not.

Here is a [version][] that fixes that:

{{< highlight cl >}}
(defvar anything-c-source-git-project-files-cache nil "(path signature cached-buffer)")
(defvar anything-c-source-git-project-files
  '((name . "Files from Current GIT Project")
    (init . (lambda ()
              (let* ((git-top-dir (magit-get-top-dir (if (buffer-file-name)
                                                         (file-name-directory (buffer-file-name))
                                                       default-directory)))
                     (top-dir (if git-top-dir
                                  (file-truename git-top-dir)
                                default-directory))
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
{{< /highlight >}}

[previous post]: /2012/03/anything-find-files-in-git-project.html
[version]: https://gist.github.com/3232191

As a diff from the previous version:

{{< highlight diff >}}
@@ -2,9 +2,12 @@
 (defvar anything-c-source-git-project-files
   '((name . "Files from Current GIT Project")
     (init . (lambda ()
-              (let* ((top-dir (file-truename (magit-get-top-dir (if (buffer-file-name)
-                                                                    (file-name-directory (buffer-file-name))
-                                                                  default-directory))))
+              (let* ((git-top-dir (magit-get-top-dir (if (buffer-file-name)
+                                                         (file-name-directory (buffer-file-name))
+                                                       default-directory)))
+                     (top-dir (if git-top-dir
+                                  (file-truename git-top-dir)
+                                default-directory))
                      (default-directory top-dir)
                      (signature (magit-rev-parse "HEAD")))
{{< /highlight >}}
