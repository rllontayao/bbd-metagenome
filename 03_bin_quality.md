BBD Metagenome: Bin Quality
================
Rowwyn Lontayao
2026-11-03

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

### Commands Used for Bin Preparation

The following commands were run in the `checkm/` directory.

``` bash
cd ~/bbd-metagenome/checkm

cat ../flye/bbd/assembly.fasta | bioawk -c fastx '{print length($seq),$name}' | sort -n | tail

samtools faidx ../flye/bbd/assembly.fasta contig_113 > ../metabat/bins_bbd/contig_113.fa

mkdir -p bins_bbd
cp ../metabat/bins_bbd/*.fa bins_bbd/

ls bins_bbd/*.fa | wc -l
```

### CheckM Analysis

CheckM was run using the `lineage_wf` workflow with six CPU threads and
40 GB of requested memory. The analysis was submitted as a SLURM job.

The script `run_checkm_bbd.sh` contained:

``` bash
#!/bin/bash
#SBATCH --time=60
#SBATCH --ntasks=6 --mem=40gb

echo "Starting checkm in $(pwd) at $(date)"

shopt -s expand_aliases
alias checkm='apptainer run -B /pvol/:/pvol /pvol/data/sif/checkm.sif checkm'

checkm lineage_wf bins_bbd out -t 6 -x fa -f checkm_results_bbd.txt

echo "Finished checkm in $(pwd) at $(date)"
```

The job was submitted using:

``` bash
sbatch run_checkm_bbd.sh
```

All 71 FASTA files were processed successfully, and the quality
assessment results were saved to `checkm_results_bbd.txt`.

### CheckM Results

| Bin        | Marker lineage      | Completeness (%) | Contamination (%) |
|------------|---------------------|-----------------:|------------------:|
| contig_113 | Cytophagales        |            97.62 |              1.64 |
| metabat.40 | Cyanobacteria       |            97.56 |              0.67 |
| metabat.41 | Bacteria            |            31.03 |              0.00 |
| metabat.69 | Bacteria            |            30.28 |              0.00 |
| metabat.2  | Bacteria            |            29.31 |              0.00 |
| metabat.29 | Deltaproteobacteria |            28.18 |              0.13 |

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

The following commands were used to inspect the CheckM results and save
the selected bin identifiers:

``` bash
head -n 20 checkm_results_bbd.txt

cat checkm_results_bbd.txt | awk '$13>15{print $1}' > bbd_topbins.txt

cat bbd_topbins.txt
wc -l bbd_topbins.txt
```

A total of **17 bins** met the completeness threshold and were recorded
in `bbd_topbins.txt`.

Although only two bins had completeness above 97%, the additional
selected bins may still provide useful information about the BBD
microbial community. The selected bins will be used for subsequent
taxonomic and functional analyses.
