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

For this example, we're going to use 

<pre style="background:#f9f9f9;color:#080808"><span style="color:#234a97">x</span> <span style="color:#794938">&lt;-</span> runif(<span style="color:#811f24;font-weight:700">1e2</span>)

microbenchmark(
  mean(<span style="color:#234a97">x</span>),
  mean.default(<span style="color:#234a97">x</span>)
)
</pre>


2.
