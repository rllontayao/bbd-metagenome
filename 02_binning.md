BBD Metagenome: Binning
================
Rowwyn Lontayao
2026-11-03

## Genome Binning Using MetaBAT2

Following metagenome assembly with Flye, the assembled contigs were
grouped into genome bins using MetaBAT2. The purpose of binning was to
separate contigs that potentially originated from the same microbial
population, allowing individual metagenome-assembled genomes (MAGs) to
be reconstructed from the Black Band Disease (BBD) microbial community.

MetaBAT2 uses sequence composition and coverage information to group
contigs. Coverage values were extracted from the Flye assembly
information file and saved as `depths_bbd.txt`. The assembly sequences
were then reordered to match the coverage file before running MetaBAT2.

### Commands Used

The following commands were run in the `metabat/` directory.

``` bash
cd ~/bbd-metagenome/metabat

cat ../flye/bbd/assembly_info.txt | grep -v '#' | awk '{OFS="\t";print $1,$3}' > depths_bbd.txt

cat depths_bbd.txt | awk '{print $1}' | xargs samtools faidx ../flye/bbd/assembly.fasta > assembly_bbd_ordered.fasta

metabat2 -i assembly_bbd_ordered.fasta -a depths_bbd.txt --cvExt -o bins_bbd/metabat --seed 5
```

### Binning Results

MetaBAT2 produced the following output:

| Binning metric                          |        Result |
|-----------------------------------------|--------------:|
| Number of bins                          |            70 |
| Total sequence length assigned to bins  | 39,561,707 bp |
| Total Flye assembly length              | 66,634,308 bp |
| Percentage of assembly assigned to bins |         59.4% |

MetaBAT2 generated 70 genome bins containing approximately 39.56 Mb of
assembled sequences, representing 59.4% of the total Flye assembly
length.

These bins represent preliminary groupings of contigs that may
correspond to individual microbial genomes. However, the number of bins
does not necessarily represent the number of microbial species present,
as some bins may be incomplete or contain sequences from multiple
organisms.

The resulting bins were saved as individual FASTA files in
`metabat/bins_bbd/` for subsequent assessment of genome completeness and
contamination using CheckM. \`\`\`
