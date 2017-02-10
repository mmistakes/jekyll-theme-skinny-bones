---
layout: media
title: "Building phylogenies from the scratch"
categories: media
excerpt: "How to download GenBank sequences for a given lineage."
ads: false
share: false
image:
  feature: branch-1600x800.jpg
  teaser: branch-400x250.jpg
  credit: Michael Rose
  creditlink: http://mademistakes.com
---

Several pipelines are currently available for mining GenBank database for a given lineage (see SUPERSMART and PHLAWD). The main goal of this tutorial is largely similar, but using an straightforward way for retrieving the sequences. I'm not doing any sort of blasting here but I'll do it in a future version.

### Materials

- R
- Libraries: taxize, plyr, RCurl, RCurl, utils and seqRFLP
- Ingroup and outgroup lineages (names)
- Genes

### Results

- A Fasta file containing all the species with published sequences (per locus)
- Sampling matrix (Accession numbers)

### Hands on!

We first need define (1) the species names (or genera, etc) that will be sampled for the outgroup, (2) ingroup , and the genes that will be analyzed.

For this example, we're going to work with the New World vulture family (Cathartidae). Four species within families Accipitridae, Pandionidae and Falconidae will be selected as as outgroups.


<pre style="background:#fff;color:#3b3b3b">library(<span style="color:#0053ff;font-weight:700">traits</span>)
library(<span style="color:#0053ff;font-weight:700">taxize</span>)
library(<span style="color:#0053ff;font-weight:700">plyr</span>)
library(<span style="color:#0053ff;font-weight:700">XML</span>)
library(<span style="color:#0053ff;font-weight:700">RCurl</span>)
library(<span style="color:#0053ff;font-weight:700">pbapply</span>)
library(<span style="color:#0053ff;font-weight:700">utils</span>)
library(<span style="color:#0053ff;font-weight:700">seqRFLP</span>)
</pre>

After loading the libraries, we have to know all the accepted species within the ingroup. We're going to downstream function (taxize) for retrieving from col (catalogue of life) all the species assigned to Cathartidae.

<pre style="background:#fff;color:#3b3b3b"><span style="color:#0053ff;font-weight:700">ingorup</span> <span style="color:#069;font-weight:700">&lt;-</span> downstream(<span style="color:#666">"Cathartidae"</span>, <span style="color:#0053ff;font-weight:700">db</span> <span style="color:#069;font-weight:700">=</span> <span style="color:#666">'col'</span>, <span style="color:#0053ff;font-weight:700">downto</span> <span style="color:#069;font-weight:700">=</span> <span style="color:#666">'species'</span>)[[<span style="color:#a8017e">1</span>]]<span style="color:#069;font-weight:700">$</span><span style="color:#0053ff;font-weight:700">childtaxa_name</span>

</pre>

The outgroup will be composed by the following four species:

<pre style="background:#fff;color:#3b3b3b"><span style="color:#0053ff;font-weight:700">outgroup</span> <span style="color:#069;font-weight:700">&lt;-</span> c(<span style="color:#666">"Gyps bengalensis"</span>, <span style="color:#666">"Pandion haliaetus"</span>, <span style="color:#666">"Falco peregrinus"</span>,<span style="color:#666">"Falco femoralis"</span>)
</pre>

Then, we just need to include the concatenate the ingroup and outgroup vectors.

<pre style="background:#fff;color:#3b3b3b"><span style="color:#0053ff;font-weight:700">sampling</span> <span style="color:#069;font-weight:700">&lt;-</span> c(<span style="color:#0053ff;font-weight:700">ingroup</span>, <span style="color:#0053ff;font-weight:700">outgroup</span>)
</pre>


At this point, we will have the names for all the valid species within the ingroup and the species that will be sampled for the outgroup. Then, we have to figure out which genes are useful for building the phylogeny. For this, I'm going use Mike Sanderson's PhyloTa, but you can use any other source (e.g. papers).

Only three genes are indicated as phylogenetic informative clusters by phyloTa for Cathartidae. Let's build a dataframe that summarizes PhyloTa information.

<pre style="background:#fff;color:#3b3b3b"><span style="color:#0053ff;font-weight:700">genes</span><span style="color:#069;font-weight:700">&lt;-</span>c(<span style="color:#666">"MB"</span>, <span style="color:#666">"cytochrome b"</span>, <span style="color:#666">"COI"</span>)
<span style="color:#0053ff;font-weight:700">range_min</span><span style="color:#069;font-weight:700">&lt;-</span>c(<span style="color:#a8017e">709</span>, <span style="color:#a8017e">1009</span>, <span style="color:#a8017e">520</span>)
<span style="color:#0053ff;font-weight:700">range_max</span><span style="color:#069;font-weight:700">&lt;-</span>c(<span style="color:#a8017e">719</span>, <span style="color:#a8017e">1145</span>, <span style="color:#a8017e">755</span>)
<span style="color:#0053ff;font-weight:700">gene_Samp</span><span style="color:#069;font-weight:700">&lt;-</span>cbind.data.frame(<span style="color:#0053ff;font-weight:700">genes</span>, <span style="color:#0053ff;font-weight:700">range_min</span>,<span style="color:#0053ff;font-weight:700">range_max</span>)

</pre>

Thanks to Scott Chamberlain, traits package is now working pretty good. ncbi_byname function retrieves the longest sequence for a locus given a species name (when available in genbank). I'm using a loop despite the multiple alternatives. The progress bar shows the percentage of genes that has been mined for all the species:

<pre style="background:#fff;color:#3b3b3b"><span style="color:#0053ff;font-weight:700">pb</span> <span style="color:#069;font-weight:700">&lt;-</span> txtProgressBar(<span style="color:#0053ff;font-weight:700">min</span> <span style="color:#069;font-weight:700">=</span> <span style="color:#a8017e">0</span>, <span style="color:#0053ff;font-weight:700">max</span> <span style="color:#069;font-weight:700">=</span> length(<span style="color:#0053ff;font-weight:700">genes</span>), <span style="color:#0053ff;font-weight:700">style</span> <span style="color:#069;font-weight:700">=</span> <span style="color:#a8017e">3</span>)

<span style="color:#0053ff;font-weight:700">sequen</span><span style="color:#069;font-weight:700">&lt;-</span><span style="color:#a535ae">NULL</span>
<span style="color:#069;font-weight:700">for</span> (<span style="color:#0053ff;font-weight:700">i</span> <span style="color:#069;font-weight:700">in</span> <span style="color:#a8017e">1</span><span style="color:#069;font-weight:700">:</span> length(<span style="color:#0053ff;font-weight:700">genes</span>)){
  <span style="color:#0053ff;font-weight:700">sequen</span>[[<span style="color:#0053ff;font-weight:700">i</span>]] <span style="color:#069;font-weight:700">&lt;-</span>ldply(ncbi_byname(<span style="color:#0053ff;font-weight:700">taxa</span>=<span style="color:#0053ff;font-weight:700">sampling</span>, <span style="color:#0053ff;font-weight:700">gene</span> <span style="color:#069;font-weight:700">=</span> as.character(<span style="color:#0053ff;font-weight:700">gene_Samp</span><span style="color:#069;font-weight:700">$</span><span style="color:#0053ff;font-weight:700">genes</span>),
                                  <span style="color:#0053ff;font-weight:700">seqrange</span> <span style="color:#069;font-weight:700">=</span> paste0(<span style="color:#0053ff;font-weight:700">gene_Samp</span><span style="color:#069;font-weight:700">$</span><span style="color:#0053ff;font-weight:700">range_min</span>[<span style="color:#0053ff;font-weight:700">i</span>],<span style="color:#666">":"</span>, <span style="color:#0053ff;font-weight:700">gene_Samp</span><span style="color:#069;font-weight:700">$</span><span style="color:#0053ff;font-weight:700">range_max</span>[<span style="color:#0053ff;font-weight:700">i</span>]), <span style="color:#0053ff;font-weight:700">getrelated</span> <span style="color:#069;font-weight:700">=</span> <span style="color:#a535ae">F</span>, <span style="color:#0053ff;font-weight:700">verbose</span> <span style="color:#069;font-weight:700">=</span> <span style="color:#a535ae">F</span>)
                      , <span style="color:#0053ff;font-weight:700">data.frame</span>)
  setTxtProgressBar(<span style="color:#0053ff;font-weight:700">pb</span>, <span style="color:#0053ff;font-weight:700">i</span>)
}
</pre>

As expected, not all species have published sequences for all gene (or any). Let's transform our list of sequences (sequen object) into a data frame:

<pre style="background:#fff;color:#3b3b3b"><span style="color:#0053ff;font-weight:700">umds</span><span style="color:#069;font-weight:700">&lt;-</span><span style="color:#a535ae">NULL</span>
<span style="color:#0053ff;font-weight:700">dat</span><span style="color:#069;font-weight:700">&lt;-</span><span style="color:#a535ae">NULL</span>
<span style="color:#0053ff;font-weight:700">pb</span> <span style="color:#069;font-weight:700">&lt;-</span> txtProgressBar(<span style="color:#0053ff;font-weight:700">min</span> <span style="color:#069;font-weight:700">=</span> <span style="color:#a8017e">0</span>, <span style="color:#0053ff;font-weight:700">max</span> <span style="color:#069;font-weight:700">=</span> length(<span style="color:#0053ff;font-weight:700">sequen</span>), <span style="color:#0053ff;font-weight:700">style</span> <span style="color:#069;font-weight:700">=</span> <span style="color:#a8017e">3</span>)
<span style="color:#069;font-weight:700">for</span> (<span style="color:#0053ff;font-weight:700">i</span> <span style="color:#069;font-weight:700">in</span> <span style="color:#a8017e">1</span><span style="color:#069;font-weight:700">:</span>length(<span style="color:#0053ff;font-weight:700">sequen</span>)){
  <span style="color:#0053ff;font-weight:700">umds</span> <span style="color:#069;font-weight:700">&lt;-</span> <span style="color:#0053ff;font-weight:700">sequen</span>[[<span style="color:#0053ff;font-weight:700">i</span>]]
  <span style="color:#0053ff;font-weight:700">namege</span><span style="color:#069;font-weight:700">&lt;-</span>cbind(<span style="color:#0053ff;font-weight:700">gene</span>=rep(<span style="color:#0053ff;font-weight:700">genes</span>[<span style="color:#0053ff;font-weight:700">i</span>], dim(<span style="color:#0053ff;font-weight:700">umds</span>)[<span style="color:#a8017e">1</span>]),<span style="color:#0053ff;font-weight:700">umds</span>)
  <span style="color:#0053ff;font-weight:700">dat</span><span style="color:#069;font-weight:700">&lt;-</span>rbind(<span style="color:#0053ff;font-weight:700">dat</span>, <span style="color:#0053ff;font-weight:700">namege</span>)
  setTxtProgressBar(<span style="color:#0053ff;font-weight:700">pb</span>, <span style="color:#0053ff;font-weight:700">i</span>)
}
</pre>

...And remove the rows without sequence information

<pre style="background:#fff;color:#3b3b3b"><span style="color:#0053ff;font-weight:700">newdat</span><span style="color:#069;font-weight:700">&lt;-</span><span style="color:#0053ff;font-weight:700">dat</span>[<span style="color:#069;font-weight:700">!</span><span style="color:#0053ff;font-weight:700">dat</span><span style="color:#069;font-weight:700">$</span><span style="color:#0053ff;font-weight:700">sequence</span><span style="color:#069;font-weight:700">=</span><span style="color:#069;font-weight:700">=</span><span style="color:#666">"NA"</span>,]

</pre>

We can then export each set of sequences to a fasta file.


<pre style="background:#fff;color:#3b3b3b"><span style="color:#0053ff;font-weight:700">genex</span><span style="color:#069;font-weight:700">&lt;-</span><span style="color:#a535ae">NULL</span>
<span style="color:#0053ff;font-weight:700">sequences</span><span style="color:#069;font-weight:700">&lt;-</span><span style="color:#ff5600">list</span>()
<span style="color:#0053ff;font-weight:700">pb</span> <span style="color:#069;font-weight:700">&lt;-</span> txtProgressBar(<span style="color:#0053ff;font-weight:700">min</span> <span style="color:#069;font-weight:700">=</span> <span style="color:#a8017e">0</span>, <span style="color:#0053ff;font-weight:700">max</span> <span style="color:#069;font-weight:700">=</span> length(unique(<span style="color:#0053ff;font-weight:700">newdat</span><span style="color:#069;font-weight:700">$</span><span style="color:#0053ff;font-weight:700">gene</span>), <span style="color:#0053ff;font-weight:700">style</span> <span style="color:#069;font-weight:700">=</span> <span style="color:#a8017e">3</span>)

<span style="color:#069;font-weight:700">for</span> (<span style="color:#0053ff;font-weight:700">i</span> <span style="color:#069;font-weight:700">in</span> <span style="color:#a8017e">1</span><span style="color:#069;font-weight:700">:</span> length(unique(<span style="color:#0053ff;font-weight:700">newdat</span><span style="color:#069;font-weight:700">$</span><span style="color:#0053ff;font-weight:700">gene</span>))){
      <span style="color:#0053ff;font-weight:700">genex</span><span style="color:#069;font-weight:700">&lt;-</span> <span style="color:#0053ff;font-weight:700">newdat</span>[<span style="color:#0053ff;font-weight:700">newdat</span><span style="color:#069;font-weight:700">$</span><span style="color:#0053ff;font-weight:700">gene</span><span style="color:#069;font-weight:700">=</span><span style="color:#069;font-weight:700">=</span>unique(<span style="color:#0053ff;font-weight:700">newdat</span><span style="color:#069;font-weight:700">$</span><span style="color:#0053ff;font-weight:700">gene</span>)[<span style="color:#0053ff;font-weight:700">i</span>],][,c(<span style="color:#a8017e">2</span>,<span style="color:#a8017e">7</span>)]
      <span style="color:#0053ff;font-weight:700">sequences</span>[[<span style="color:#0053ff;font-weight:700">i</span>]] <span style="color:#069;font-weight:700">&lt;-</span> dataframe2fas(<span style="color:#0053ff;font-weight:700">genex</span>)
      write.fasta(dataframe2fas(<span style="color:#0053ff;font-weight:700">genex</span>), <span style="color:#0053ff;font-weight:700">file</span>=paste0(unique(<span style="color:#0053ff;font-weight:700">newdat</span><span style="color:#069;font-weight:700">$</span><span style="color:#0053ff;font-weight:700">gene</span>)[<span style="color:#0053ff;font-weight:700">i</span>], <span style="color:#666">"Cathartidae"</span>, <span style="color:#666">".fasta"</span>))
      setTxtProgressBar(<span style="color:#0053ff;font-weight:700">pb</span>, <span style="color:#0053ff;font-weight:700">i</span>)
                     }
</pre>

The unaligned fasta files will be in the working directory but we still need to retrieve the accession number for each sequence. The following function does some scrapping based on the gi numbers.

<pre style="background:#fff;color:#3b3b3b"><span style="color:#0053ff;font-weight:700">sea</span><span style="color:#069;font-weight:700">&lt;-</span> <span style="color:#0053ff;font-weight:700">newdat</span><span style="color:#069;font-weight:700">$</span><span style="color:#0053ff;font-weight:700">gi_no</span>
<span style="color:#0053ff;font-weight:700">ja</span><span style="color:#069;font-weight:700">&lt;-</span><span style="color:#a535ae">NULL</span>
<span style="color:#0053ff;font-weight:700">ji</span><span style="color:#069;font-weight:700">&lt;-</span><span style="color:#a535ae">NULL</span>
<span style="color:#0053ff;font-weight:700">ju</span><span style="color:#069;font-weight:700">&lt;-</span><span style="color:#a535ae">NULL</span>

<span style="color:#21439c">m.boot</span> <span style="color:#069;font-weight:700">&lt;-</span> <span style="color:#069;font-weight:700">function</span>(<span style="color:#0053ff;font-weight:700">sea</span>) {
  tryCatch({
    <span style="color:#0053ff;font-weight:700">dir</span><span style="color:#069;font-weight:700">&lt;-</span>paste0(<span style="color:#666">"https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=nucleotide&amp;id="</span>, <span style="color:#0053ff;font-weight:700">sea</span>, <span style="color:#666">"&amp;rettype=gb&amp;retmode=xml"</span>)
    <span style="color:#0053ff;font-weight:700">xml</span> <span style="color:#069;font-weight:700">&lt;-</span> getURL(<span style="color:#0053ff;font-weight:700">dir</span>)
    <span style="color:#0053ff;font-weight:700">doc</span> <span style="color:#069;font-weight:700">&lt;-</span> xmlTreeParse(<span style="color:#0053ff;font-weight:700">xml</span>, <span style="color:#0053ff;font-weight:700">useInternalNodes</span> <span style="color:#069;font-weight:700">=</span> <span style="color:#a535ae">T</span>)
    <span style="color:#0053ff;font-weight:700">xmltop</span> <span style="color:#069;font-weight:700">=</span> xmlRoot(<span style="color:#0053ff;font-weight:700">doc</span>)
    <span style="color:#0053ff;font-weight:700">ja</span>[<span style="color:#0053ff;font-weight:700">i</span>]<span style="color:#069;font-weight:700">&lt;-</span>xpathApply(<span style="color:#0053ff;font-weight:700">xmltop</span>, <span style="color:#666">"//GBInterval_accession"</span>, <span style="color:#0053ff;font-weight:700">xmlValue</span>)[[<span style="color:#a8017e">1</span>]]
  }
  , <span style="color:#0053ff;font-weight:700">error</span>=<span style="color:#069;font-weight:700">function</span>(<span style="color:#0053ff;font-weight:700">e</span>){})
}

</pre>


Let's apply the function and create a column on our dataset named acc_no

<pre style="background:#fff;color:#3b3b3b"><span style="color:#0053ff;font-weight:700">accn</span><span style="color:#069;font-weight:700">&lt;-</span>unlist(pblapply(<span style="color:#0053ff;font-weight:700">sea</span>,<span style="color:#0053ff;font-weight:700">m.boot</span>))
<span style="color:#0053ff;font-weight:700">newdat</span><span style="color:#069;font-weight:700">$</span><span style="color:#0053ff;font-weight:700">acc_no</span><span style="color:#069;font-weight:700">&lt;-</span><span style="color:#0053ff;font-weight:700">accn</span>
</pre>

Is just matter of building a sampling matrix

<pre style="background:#fff;color:#3b3b3b"><span style="color:#0053ff;font-weight:700">genexx</span><span style="color:#069;font-weight:700">&lt;-</span><span style="color:#a535ae">NULL</span>
<span style="color:#069;font-weight:700">for</span> (<span style="color:#0053ff;font-weight:700">i</span> <span style="color:#069;font-weight:700">in</span> <span style="color:#a8017e">1</span><span style="color:#069;font-weight:700">:</span> length(unique(<span style="color:#0053ff;font-weight:700">newdat</span><span style="color:#069;font-weight:700">$</span><span style="color:#0053ff;font-weight:700">gene</span>))){
  <span style="color:#0053ff;font-weight:700">genexx</span>[[<span style="color:#0053ff;font-weight:700">i</span>]]<span style="color:#069;font-weight:700">&lt;-</span> <span style="color:#0053ff;font-weight:700">newdat</span>[<span style="color:#0053ff;font-weight:700">newdat</span><span style="color:#069;font-weight:700">$</span><span style="color:#0053ff;font-weight:700">gene</span><span style="color:#069;font-weight:700">=</span><span style="color:#069;font-weight:700">=</span>unique(<span style="color:#0053ff;font-weight:700">newdat</span><span style="color:#069;font-weight:700">$</span><span style="color:#0053ff;font-weight:700">gene</span>)[<span style="color:#0053ff;font-weight:700">i</span>],][,c(<span style="color:#a8017e">2</span>,<span style="color:#a8017e">8</span>)]
}


<span style="color:#0053ff;font-weight:700">genesampling</span><span style="color:#069;font-weight:700">&lt;-</span> Reduce(<span style="color:#069;font-weight:700">function</span>(<span style="color:#0053ff;font-weight:700">x</span>, <span style="color:#0053ff;font-weight:700">y</span>) merge(<span style="color:#0053ff;font-weight:700">x</span>, <span style="color:#0053ff;font-weight:700">y</span>,<span style="color:#0053ff;font-weight:700">by</span>=<span style="color:#666">"taxon"</span>, <span style="color:#0053ff;font-weight:700">all</span>=<span style="color:#a535ae">TRUE</span>), <span style="color:#0053ff;font-weight:700">genexx</span>)
colnames(<span style="color:#0053ff;font-weight:700">genesampling</span>)[<span style="color:#069;font-weight:700">-</span><span style="color:#a8017e">1</span>]<span style="color:#069;font-weight:700">&lt;-</span>as.character(unique(<span style="color:#0053ff;font-weight:700">newdat</span><span style="color:#069;font-weight:700">$</span><span style="color:#0053ff;font-weight:700">gene</span>))

</pre>

We just need to write the matrix into our working directory

<pre style="background:#fff;color:#3b3b3b">write.csv(<span style="color:#0053ff;font-weight:700">genesampling</span>, <span style="color:#666">"Sampling matrix.csv"</span>)

</pre>
