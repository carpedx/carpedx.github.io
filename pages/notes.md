---
layout: page
title: 笔记
description: 一天记录一点点
keywords: 笔记, note
comments: true
menu: 笔记
permalink: /notes/
---

> 一天记录一点点

<ul class="listing">
{% for note in site.notes %}
    {% if note.title != "Note Template" %}
    <li class="listing-item"><a href="{{ site.url }}{{ note.url }}">{{ note.title }}</a></li>
    {% endif %}
{% endfor %}
</ul>