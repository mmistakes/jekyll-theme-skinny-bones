---
layout: archive
title: "Articles"
date: 2014-05-30T11:39:03-04:00
modified:
excerpt: "Summary of published papers. Click for details."
tags: []
image:
  feature:
  teaser:
---

<div class="tiles">
{% for post in site.categories.articles %}
  {% include post-grid.html %}
{% endfor %}
</div><!-- /.tiles -->