---
layout: post
title: Importing a Blosxom blog into Jekyll
---

As [mentioned][], I recently decided to move my blog from a
self-hosted, [Blosxom][]-driven mostly-manual set up to [github pages][].

This involved these main steps:

 * Set up a github repo to hold the templates and source text
 * Migrate templates from Blosxom's templating language to
   [Jekyll][]/[Liquid][]
 * Import the content

I won't cover the first two in detail here. Setting up a repository
for pages is well documented by github, and migrating the templates
was relatively straightforward--I used the [code][] behind [Simon
Harris][]'s blog as a starting point. (Getting the [archive
page][] working was slightly more interesting. I'll write more on this
later.)

There were two parts to importing the content. Firstly, the directory
layout expected by Jekyll is slightly different to that I was using in
Blosxom.

Here is what I had:

    .
    |-- 2009
    |   `-- 04
    |       |-- an-interesting-story.txt
    |       `-- something-else.txt
    |-- 2010
    |   |-- 01
    |   |   |-- happy-new-year.txt
    |   |   `-- headache.txt
    |   `-- 08
    |       `-- migrating-blog.txt

Jekyll wants a much flatter directory layout, with all the files in a
single directory and the date as part of the file name:

    .
    `-- _posts
        |-- 2009-04-01-an-interesting-story.md
        |-- 2009-04-19-something-else.md
        |-- 2010-01-01-happy-new-year.md
        |-- 2010-01-02-headache.md
        `-- 2010-08-04-migrating-blog.md

The trick was that Jekyll wanted a day, but I only encoded the year
and month in my Blosxom file structure. Luckily, I was using the
Blosxom `entries_index` plugin, which stores Unix-style timestamps for
every entry it publishes. So I wrote a little [Clojure][] program to
read the `entries_index` cache and derive a Jekyll-style file name for
every entry:

{% highlight clj %}
(use 'clojure.contrib.str-utils)
(use 'clojure.contrib.duck-streams)

(import 'java.util.Date 'java.text.SimpleDateFormat)

(def entry-index
  (read-lines (first *command-line-args*)))

(defn parse-line [line]
  (let [[_ filename timestamp] (re-matches #".*'(.+)'.*\s+(\d+).*" line)]
    {:filepath filename :timestamp timestamp}))

(defn date [timestamp] (Date. (* 1000 (Long/valueOf timestamp))))
(defn date-str [date] (. (SimpleDateFormat. "yyyy-MM-dd") format date))
(defn filename [path] (last (re-split #"/" path)))
(defn md-ext [s] (re-sub #".txt$" ".md" s))
(defn valid? [line] (not (nil? (:timestamp line))))

(defn target-file-name [entry]
  (str (date-str (date (entry :timestamp))) "-" (md-ext (filename (entry :filepath)))))

(def entries (filter valid? (map parse-line entry-index)))

(defn copy-command [entry]
  (str "cp " (entry :filepath) " " (target-file-name entry)))

(println (str-join "\n" (map copy-command entries)))
{% endhighlight %}

Note that this program doesn't actually do anything, it just outputs a
bunch of "cp" commands that you can feed into a shell.

The second step is to add a block of YAML "front matter" to each file
that Jekyll uses to parse the file and generate the appropriate
output. This front matter is of the form:

    ---
    layout: post
    title: Blog migration
    ---

This tells Jekyll which template to use, and what to use for a title.
The Blosxom source files don't contain any such front matter, but do
have the post's title as their first line. A simple bit of `sed`
wrote the appropriate opening lines of each file:

    1,1 s/\([^-].*\)/---\
    layout: post\
    title: \1\
    ---/g

I invoked it like this:

    for f in `ls  _posts/*`
        do sed -f ~/Projects/migrate-blosxom-to-jekyll/insert_front_matter.sed -i "" $f
    done

And that was more or less that! The above code is available on github at
[http://github.com/mrowe/migrate-blosxom-to-jekyll](http://github.com/mrowe/migrate-blosxom-to-jekyll),
and of course the entire content of my blog is at
[http://github.com/mrowe/mrowe.github.com](http://github.com/mrowe/mrowe.github.com).

[mentioned]: /2010/08/blog-migration.html
[Blosxom]: http://www.blosxom.com/
[github pages]: http://pages.github.com/
[Jekyll]: http://wiki.github.com/mojombo/jekyll/
[Liquid]: http://www.liquidmarkup.org/
[Clojure]: http://clojure.org/
[Simon Harris]: http://www.harukizaemon.com/
[code]: http://github.com/harukizaemon/www.harukizaemon.com
[archive page]: /archive.html
