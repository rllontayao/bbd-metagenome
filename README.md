
# The Metagenome of Black Band Disease

This repository documents the assembly, genome binning, taxonomic classification and functional analysis of nanopore metagenomic sequencing data from Black Band Disease (BBD) affecting the coral *Acropora hyacinthus*.

The analysis was completed as part of [Workshop 4](https://mb5370.bioinformatics.guide/workshop4.html) of the MB5370 Marine Genomics module at James Cook University.

## Project Overview

The aim of this analysis was to investigate the microbial community associated with BBD by reconstructing microbial genomes, identifying their taxonomic classifications and examining their potential metabolic functions.

The bioinformatics workflow involved six main stages:

1. Metagenome assembly using Flye
2. Genome binning using MetaBAT2
3. Genome quality assessment using CheckM
4. Taxonomic classification using GTDB-Tk
5. Gene prediction and annotation using Prokka
6. Metabolic pathway inference using MinPath

## Key Findings

The Flye assembly produced 66,634,308 bp of assembled sequences across 7,972 contigs.

MetaBAT2 recovered 70 genome bins, representing approximately 59.4% of the assembled sequence length. Following CheckM quality assessment, 17 genome bins with estimated completeness greater than 15% were selected for further analysis.

GTDB-Tk identified a cyanobacterial genome bin, `metabat.40`, belonging to the genus *Roseofilum*, which is associated with coral Black Band Disease.

Metabolic pathway analysis using MinPath identified all four gene families associated with photosynthesis light reactions (PWY-101) in `metabat.40`, providing evidence of photosynthetic potential.

In contrast, only one of three gene families associated with tetrathionate reduction (PWY-5360) was identified in `metabat.69`, and MinPath did not retain the pathway. Therefore, the results do not provide strong evidence for a complete tetrathionate reduction pathway in this bin.

These findings describe predicted genomic capabilities rather than direct measurements of microbial activity.

## Analysis Documentation

Each link below opens the knitted Markdown documentation for the corresponding analysis step.

1. [Assembly](01_assembly.md)
2. [Binning](02_binning.md)
3. [Bin Quality](03_bin_quality.md)
4. [Taxonomy](04_taxonomy.md)
5. [Gene Annotation](05_gene_annotation.md)
6. [Metabolic Pathways](06_metabolic_pathways.md)

The complete analysis is also available as a single HTML document generated from `index.Rmd`.

## Repository Structure

| Analysis step | R Markdown file | Working directory | Software |
|---|---|---|---|
| Assembly | `01_assembly.Rmd` | `flye/` | Flye |
| Binning | `02_binning.Rmd` | `metabat/` | MetaBAT2 |
| Bin quality | `03_bin_quality.Rmd` | `checkm/` | CheckM |
| Taxonomy | `04_taxonomy.Rmd` | `gtdbtk/` | GTDB-Tk |
| Gene annotation | `05_gene_annotation.Rmd` | `prokka/` | Prokka |
| Metabolic pathways | `06_metabolic_pathways.Rmd` | `minpath/` | MinPath |

The `index.Rmd` file combines the six analysis documents into `index.html`.

Large sequencing datasets, assemblies and genome bin files are not included in the GitHub repository. The repository contains the analysis documentation, relevant scripts and selected summary outputs.
