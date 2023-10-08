---
layout: page
title: notes
description: notes 笔记页
keywords: notes
comments: false
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