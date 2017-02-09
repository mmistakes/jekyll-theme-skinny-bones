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


<pre style="background:#000;color:#f8f8f8"><span style="color:#3e87e3">x</span> <span style="color:#e28964">&lt;-</span> runif(<span style="color:#3387cc">1e2</span>)

microbenchmark(
  mean(<span style="color:#3e87e3">x</span>),
  mean.default(<span style="color:#3e87e3">x</span>)
)
</pre>
