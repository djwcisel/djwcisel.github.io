---
layout: post
title:  "Acquiring the Data"
date:   2018-02-28 11:36:16 -0500
categories: jekyll update
---
NCBI's Nucleotide database was searched using the following perl code (adapted from: xxx) on 2/28/2018
This code retrieves all mRNA sequences for txid7898 (Actinopterygii - all ray-finned fishes).

{% highlight perl %}
use LWP::Simple;
$query = 'txid7898[Organism:exp]+AND+biomol+mrna[prop]';

#assemble the esearch URL
$base = 'https://eutils.ncbi.nlm.nih.gov/entrez/eutils/';
$url = $base . "esearch.fcgi?db=nucleotide&term=$query&usehistory=y";

#post the esearch URL
$output = get($url);

#parse WebEnv, QueryKey and Count (# records retrieved)
$web = $1 if ($output =~ /<WebEnv>(\S+)<\/WebEnv>/);
$key = $1 if ($output =~ /<QueryKey>(\d+)<\/QueryKey>/);
$count = $1 if ($output =~ /<Count>(\d+)<\/Count>/);

#open output file for writing
open(OUT, ">txid7898.fna") || die "Can't open file!\n";

#retrieve data in batches of 500
$retmax = 500;
for ($retstart = 0; $retstart < $count; $retstart += $retmax) {
        $efetch_url = $base ."efetch.fcgi?db=nucleotide&WebEnv=$web";
        $efetch_url .= "&query_key=$key&retstart=$retstart";
        $efetch_url .= "&retmax=$retmax&rettype=fasta&retmode=text";
        $efetch_out = get($efetch_url);
        print OUT "$efetch_out";
}
close OUT;
{% endhighlight %}

This retrieved a total number of xxx sequences.

However, while some Transcriptome Shotgun Assembled (TSA) sequences were retrieved, not all of the TSA
databases are found in NCBI's Nucleotide database. Therefore, 
