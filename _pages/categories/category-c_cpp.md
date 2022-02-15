---
title: "C/C++"
layout: archive
permalink: /categories/c_cpp
author_profile: true
sidebar_main: true
---

{% assign posts = site.categories.['C/C++'] %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}