---
title: "Softeer"
layout: archive
permalink: /categories/softeer
author_profile: true
sidebar_main: true
---

{% assign posts = site.categories.['Softeer'] %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
