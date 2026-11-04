BBD Metagenome: Bin Quality
================
Rowwyn Lontayao
2026-11-04

## Genome Bin Quality Assessment Using CheckM

Following genome binning with MetaBAT2, the quality of the reconstructed
genome bins was assessed using CheckM. CheckM estimates genome
completeness and contamination based on the presence and copy number of
lineage-specific marker genes.

The purpose of this step was to identify genome bins suitable for
further taxonomic classification and functional analysis of the Black
Band Disease (BBD) microbial community.

### Preparation of Genome Bins

The longest contig from the Flye assembly, `contig_113` (4,706,700 bp),
was extracted as a separate FASTA file following the workshop
instructions. This allowed the longest contig to be assessed
independently alongside the MetaBAT2 bins.

The 70 MetaBAT2 bins and the additional contig were copied into the
`checkm/bins_bbd/` directory, resulting in 71 FASTA files for quality
assessment.

### CheckM Results

CheckM was run using the `lineage_wf` workflow to estimate genome
completeness and contamination.

The CheckM completeness and contamination estimates for the 17 genome
bins selected for further analysis are presented below, arranged from
highest to lowest completeness.

``` r
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
library(knitr)

checkm_lines <- readLines("checkm/checkm_results_bbd.txt")

# Keep rows beginning with a genome bin identifier
checkm_lines <- grep(
  "^\\s*(contig_|metabat\\.)",
  checkm_lines,
  value = TRUE
)

# Extract the bin, marker lineage and quality statistics
checkm_table <- lapply(checkm_lines, function(line) {
  parts <- strsplit(trimws(line), "\\s{2,}")[[1]]

  data.frame(
    Bin = parts[1],
    Marker_lineage = parts[2],
    Completeness = as.numeric(parts[12]),
    Contamination = as.numeric(parts[13])
  )
}) %>%
  bind_rows()

# Select the bins used for subsequent analyses
checkm_summary <- checkm_table %>%
  filter(Completeness > 15) %>%
  arrange(desc(Completeness)) %>%
  mutate(
    Marker_lineage = sub(" \\(UID[0-9]+\\)$", "", Marker_lineage),
    Marker_lineage = sub("^[a-z]__", "", Marker_lineage)
  )

kable(
  checkm_summary,
  col.names = c(
    "Genome bin",
    "Marker lineage",
    "Completeness (%)",
    "Contamination (%)"
  ),
  digits = 2,
  caption = "CheckM quality assessment of the 17 selected genome bins"
)
```

| Genome bin | Marker lineage      | Completeness (%) | Contamination (%) |
|:-----------|:--------------------|-----------------:|------------------:|
| contig_113 | Cytophagales        |            97.62 |              1.64 |
| metabat.40 | Cyanobacteria       |            97.56 |              0.67 |
| metabat.41 | Bacteria            |            31.03 |              0.00 |
| metabat.69 | Bacteria            |            30.28 |              0.00 |
| metabat.2  | Bacteria            |            29.31 |              0.00 |
| metabat.29 | Deltaproteobacteria |            28.18 |              0.13 |
| metabat.21 | Clostridiales       |            22.60 |              0.00 |
| metabat.34 | Bacteria            |            22.41 |              0.00 |
| metabat.3  | Bacteria            |            21.55 |              0.00 |
| metabat.39 | Bacteria            |            21.43 |              0.00 |
| metabat.1  | Proteobacteria      |            21.00 |              0.00 |
| metabat.33 | Deltaproteobacteria |            20.79 |              0.00 |
| metabat.31 | Bacteria            |            19.73 |              0.00 |
| metabat.55 | Bacteria            |            18.97 |              0.00 |
| metabat.5  | Bacteria            |            17.54 |              0.00 |
| metabat.4  | Bacteria            |            17.24 |              0.00 |
| metabat.44 | Clostridiales       |            16.31 |              0.00 |

CheckM quality assessment of the 17 selected genome bins

The two most complete bins were `contig_113` (97.62%) and `metabat.40`
(97.56%), both with estimated contamination below 2%. The next most
complete bin, `metabat.41`, had only 31.03% completeness.

These results suggest that the first two bins contain substantially more
complete genomic information than the remaining bins. Their marker
lineages suggest associations with Cytophagales and Cyanobacteria,
respectively, although further taxonomic classification is required.

### Selection of Bins for Further Analysis

Following the workshop instructions, bins with estimated completeness
greater than 15% were selected for subsequent analysis.

A total of **17 bins** met the completeness threshold and were recorded
in `bbd_topbins.txt`.

Although only two bins had completeness above 97%, the additional
selected bins may still provide useful information about the BBD
microbial community. These selected bins were subsequently used for
taxonomic classification with GTDB-Tk and functional annotation with
Prokka and MinPath.

### Processing Log

First, the longest contig was identified from the Flye assembly and
extracted as a separate FASTA file:

``` bash
cd ~/bbd-metagenome/checkm

cat ../flye/bbd/assembly.fasta | bioawk -c fastx '{print length($seq),$name}' | sort -n | tail

samtools faidx ../flye/bbd/assembly.fasta contig_113 > ../metabat/bins_bbd/contig_113.fa
```

The MetaBAT2 bins and the longest contig were copied into the CheckM
input directory:

``` bash
cp ../metabat/bins_bbd/*.fa bins_bbd/
```

CheckM was then run through the SLURM scheduler using the script
[run_checkm_bbd.sh](checkm/run_checkm_bbd.sh).

The main analysis command in the script was:

``` bash
checkm lineage_wf bins_bbd out -t 6 -x fa -f checkm_results_bbd.txt
```

The `lineage_wf` option ran the lineage-specific marker gene analysis,
`-t 6` used six processing threads, `-x fa` specified the FASTA file
extension, and `-f` saved the results to `checkm_results_bbd.txt`.

The job was submitted using:

``` bash
sbatch run_checkm_bbd.sh
```

The CheckM results were examined, and bins with completeness greater
than 15% were selected:

``` bash
head -n 20 checkm_results_bbd.txt

cat checkm_results_bbd.txt | awk '$13>15{print $1}' > bbd_topbins.txt

wc -l bbd_topbins.txt
```

The resulting `bbd_topbins.txt` file contained 17 bin identifiers for
the subsequent analyses.
