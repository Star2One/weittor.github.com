---
layout: default
title: About
permalink: /about/
---

Site:   {{ site.description }}

Email:  {{ site.email }}

{% if site.weibo %}
Weibo：<http://weibo.com/{{ site.weibo }}>
{% endif %}

RSS：[{{ site.url }}{{ '/rss.xml' }}](/rss.xml)



