---
layout: post
title: Jekyll archives grouped by date
---

One thing [Jekyll][] doesn't provide out of the box (as fas I can
tell) is any sort of archive functionality. (Aside: I really like what
[Tumblr][] does for [archives][].)

I would have liked something a bit more flexible, but for now this
site's [archive][] displays a list of *all* entries grouped by year.
Here's the template code I'm using:

{% highlight html %}
<h2>Archives</h2>
<ul>
  {{'{'}}% for post in site.posts %}

    {{'{'}}% unless post.next %}
      <h3>{{'{'}}{ post.date | date: '%Y' }}</h3>
    {{'{'}}% else %}
      {{'{'}}% capture year %}{{'{'}}{{'{'}} post.date | date: '%Y' }}{{'{'}}% endcapture %}
      {{'{'}}% capture nyear %}{{'{'}}{{'{'}} post.next.date | date: '%Y' }}{{'{'}}% endcapture %}
      {{'{'}}% if year != nyear %}
        <h3>{{'{'}}{{'{'}} post.date | date: '%Y' }}</h3>
      {{'{'}}% endif %}
    {{'{'}}% endunless %}

    <li>{{'{'}}{{'{'}} post.date | date:"%b" }} <a href="{{'{'}}{{'{'}} post.url }}">{{'{'}}{{'{'}} post.title }}</a></li>
  {{'{'}}% endfor %}
</ul>
{% endhighlight %}

which was shamelessly ripped off from
[http://blog.tracefunc.com/2009/12/04/jekyll-custom-liquid-tags/](http://blog.tracefunc.com/2009/12/04/jekyll-custom-liquid-tags/)

[Jekyll]: http://github.com/mojombo/jekyll
[Tumblr]: http://tumblr.com/
[archives]: http://www.therowes.id.au/archive
[archive]: /archive.html
