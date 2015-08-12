---
layout: archive
permalink: /
title: "Latest Posts"
---

## Hi I´m Adel
I happend to be trained in sociology, but have a much wider interest in the social sciences: economics, political science, and development. 

<div class="tiles">
{% for post in site.posts %}
	{% include post-grid.html %}
{% endfor %}
</div><!-- /.tiles -->
