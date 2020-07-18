---
layout: default

---

<div class="index_titles">

# Hello!

---

<div class="intro-table">
<table>
<tr>
<td>
<p>
This is a blog about different software development topics that I find interesting, had a hard time finding exactly what I was looking for, or want to reference later.
</p>

I'm [Sabrina](about.md) aka [Sab](about.md).
Hopefully you find some use out of this blog.

</td>
<td>
<a href="/about/"><img class="intro-me" src="/assets/me.jpg" alt="Me"/>
</td>
</tr>
</table>
</div>

<span class="big-rule">
<hr/>
</span>

# Recent Posts

{% for post in site.posts limit:4 %}
<div class="backing">
<div class="post-preview-10">

## [{{post.title}}]({{post.url}})

<div class="post-preview-30">

{{post.excerpt}}

[[Read more...]({{post.url}})]

</div>
</div>
</div>

{% if forloop.last == false %}
---
{% endif %}

{% endfor %}
<span class="big-rule">
<hr/>
</span>

# All Posts

{% for category in site.categories %}

{% capture category_name %}{{ category | first }}{% endcapture %}

## {{category_name}}
<div class="post-preview-30">
{% for post in site.categories[category_name] %}

» [{{post.title}}]({{post.url}})

{% endfor %}
</div>

{% endfor %}
