---
layout: media
title: "Building phylogenies from the scratch"
categories: media
excerpt: "How to download GenBank sequences for a given lineage."
ads: false
share: false
image:
  feature: lock-1600x800.jpg
  teaser: lock-400x250.jpg
  credit: Cristian Román
---

### Introduction!

Several pipelines are currently available for mining GenBank for a given taxon set (see SUPERSMART and PHLAWD). However, the main goal of this tutorial is to retrieve all the available sequences for a series of species within R.    

### Hands on!

1. We need to know (1) which species (or genera,etc) will be sampled as outgroup, (2) the genes that will be used, and (3) the ingroup taxa.


<!-- HTML generated using hilite.me --><div style="background: #ffffff; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #888888">x &lt;- runif(1e2)</span>

<span style="color: #888888">microbenchmark(</span>
<span style="color: #888888">  mean(x),</span>
<span style="color: #888888">  mean.default(x)</span>
<span style="color: #888888">)</span>
</pre></div>
