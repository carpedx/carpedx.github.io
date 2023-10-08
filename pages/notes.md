---
layout: page
title: notes
description: notes 笔记页
keywords: notes
comments: false
mermaid: true
menu: 笔记
permalink: /notes/
---

> 一天记录一点点

<ul class="listing">
{% for item in site.notes %}
{% if item.title != "Note Template" %}
<li class="listing-item" tags="{% for tag in item.tags %}{{ tag }} {% endfor %}">
  <a href="{{ site.url }}{{ item.url }}">{{ item.title }}</a>
</li>
{% endif %}
{% endfor %}
</ul>