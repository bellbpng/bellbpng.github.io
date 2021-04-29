---
title: "Posts by Category"
layout: archive
permalink: /categories/
author_profile: true
---
{% assign posts = site.categories %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
