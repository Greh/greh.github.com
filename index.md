---
layout: default
---

## Blog Posts
<ul class="posts">
{% for post in site.posts %}
<li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>

## Current Projects

<ul class="posts">
<li><span>Hardware</span> &raquo; <a href="/cockroaches">Cockroaches</a></li>
<li><span>Software</span> &raquo; <a href="http://github.com/greh/coconuts">Math in Python</a></li>
</ul>

## About

<ul class="posts">
<li><a href="http://github.com/greh">Github</a></li>
<li><a href="/resume">Resume</a></li>
<li><a href="http://opensourcebridge.org/sessions/1365">Open Source Bridge Talk</a></li>
</ul>

