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

We first need define (1) the species names (or genera, etc) that will be sampled for the outgroup, (2) ingroup , and the genes that will be analyzed.

For this example, we're going to work with the New World vulture family (Cathartidae). Four species within families Accipitridae, Pandionidae and Falconidae will be selected as as outgroups.

Libraries taxize, plyr, utils and seqRFLP are required for this tutorial.

<pre style="background:#fff;color:#3b3b3b">library(<span style="color:#0053ff;font-weight:700">traits</span>)
library(<span style="color:#0053ff;font-weight:700">taxize</span>)
library(<span style="color:#0053ff;font-weight:700">plyr</span>)
library(<span style="color:#0053ff;font-weight:700">utils</span>)
library(<span style="color:#0053ff;font-weight:700">seqRFLP</span>)
</pre>

After loading the libraries, we have to know all the accepted species within the ingroup. We're going to downstream function (taxize) for retrieving from col (catalogue of life) all the species assigned to Cathartidae.

<pre style="background:#fff;color:#3b3b3b"><span style="color:#0053ff;font-weight:700">Ingroup</span> <span style="color:#069;font-weight:700">&lt;-</span> downstream(<span style="color:#666">"taxize"</span>, <span style="color:#0053ff;font-weight:700">db</span> <span style="color:#069;font-weight:700">=</span> <span style="color:#666">'col'</span>, <span style="color:#0053ff;font-weight:700">downto</span> <span style="color:#069;font-weight:700">=</span> <span style="color:#666">'species'</span>)

</pre>

The outgroup will be composed by the following four species:

<pre style="background:#fff;color:#3b3b3b"><span style="color:#0053ff;font-weight:700">outgroup</span> <span style="color:#069;font-weight:700">&lt;-</span> c(<span style="color:#666">"Gyps bengalensis"</span>, <span style="color:#666">"Pandion haliaetus"</span>, <span style="color:#666">"Falco peregrinus"</span>,<span style="color:#666">"Falco femoralis"</span>)
</pre>

Then, we just need to include the concatenate both ingroup and outgroup vectors.

<pre style="background:#fff;color:#3b3b3b"><span style="color:#0053ff;font-weight:700">sampling</span> <span style="color:#069;font-weight:700">&lt;-</span> c(<span style="color:#0053ff;font-weight:700">ingroup</span>, <span style="color:#0053ff;font-weight:700">outgroup</span>)
</pre>
