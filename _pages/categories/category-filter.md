---
title: "Filter"
layout: archive
permalink: /categories/filter
author_profile: true
sidebar_main: true
---

{% assign posts = site.categories.['Filter'] %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}