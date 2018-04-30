---
layout: default
title: Georgia Reh
---

## Blog Posts
<ul class="posts">
{% for post in site.posts %}
<li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>

## About

<ul class="posts">
<li><a href="http://github.com/greh">Github</a></li>
<li><a href="http://twitter.com/georgiareh">Twitter</a></li>
</ul>

