---
layout: archive
permalink: /
title: "Latest Posts"
image: kelp.JPG
---

<div class="tiles">
{% for post in site.posts %}
	{% include post-grid.html %}
{% endfor %}
</div><!-- /.tiles -->