---
title: "C언어 기초"
layout: archive
permalink: /categories/c_basic
author_profile: true
sidebar_main: true
---


{% assign posts = site.categories.['C language'] %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}