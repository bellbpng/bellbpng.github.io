---
title: "Object-Oriented Programming"
layout: archive
permalink: /categories/oop
author_profile: true
sidebar_main: true
---

{% assign posts = site.categories.['Object-Oriented Programming'] %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}