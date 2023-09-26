---
layout: page
title: Links
description: 没有链接的博客是孤独的
keywords: 友情链接
comments: true
menu: 链接
permalink: /links/
---

> Docs

<ul>
{% for link in site.data.links %}
  {% if link.src == 'doc' %}
  <li><a href="{{ link.url }}" target="_blank">{{ link.name}}</a></li>
  {% endif %}
{% endfor %}
</ul>

> Learning Website

<ul>
{% for link in site.data.links %}
  {% if link.src == 'study' %}
  <li><a href="{{ link.url }}" target="_blank">{{ link.name}}</a></li>
  {% endif %}
{% endfor %}
</ul>

> Scientific Internet Access

<ul>
{% for link in site.data.links %}
  {% if link.src == 'vpn' %}
  <li><a href="{{ link.url }}" target="_blank">{{ link.name}}</a></li>
  {% endif %}
{% endfor %}
</ul>

> Linux Related Software

<ul>
{% for link in site.data.links %}
  {% if link.src == 'linux' %}
  <li><a href="{{ link.url }}" target="_blank">{{ link.name}}</a></li>
  {% endif %}
{% endfor %}
</ul>

> Docker Related Software

<ul>
{% for link in site.data.links %}
  {% if link.src == 'docker' %}
  <li><a href="{{ link.url }}" target="_blank">{{ link.name}}</a></li>
  {% endif %}
{% endfor %}
</ul>

> Media Format Conversion

<ul>
{% for link in site.data.links %}
  {% if link.src == 'media' %}
  <li><a href="{{ link.url }}" target="_blank">{{ link.name}}</a></li>
  {% endif %}
{% endfor %}
</ul>