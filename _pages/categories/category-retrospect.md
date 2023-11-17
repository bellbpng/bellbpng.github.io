---
title: "retrospect"
layout: archive
permalink: /categories/retrospect
author_profile: true
sidebar_main: true
---

{% assign posts = site.career.['회고'] %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}