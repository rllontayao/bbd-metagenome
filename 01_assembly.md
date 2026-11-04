BBD Metagenome: Assembly
================
Rowwyn Lontayao
2026-11-04

## Metagenome Assembly Using Flye

The Black Band Disease (BBD) metagenomic sequencing reads were assembled
using Flye, a genome assembler designed for long-read sequencing data.
The purpose of this step was to combine overlapping nanopore reads into
longer contiguous sequences (contigs), which could then be used to
investigate the microbial community associated with BBD.

Flye was run in metagenome mode (`--meta`) because the sequencing data
contained DNA from multiple microorganisms. The assembly was performed
using 12 threads and the high-quality nanopore read setting
(`--nano-hq`).

### Assembly Results

The Flye assembly produced the following results:

| Assembly metric       |        Result |
|-----------------------|--------------:|
| Total assembly length | 66,634,308 bp |
| Number of contigs     |         7,972 |
| Contig N50            |     12,994 bp |
| Largest contig        |  4,706,700 bp |
| Mean coverage         |           32× |

The assembly contained 7,972 contigs with a combined length of
approximately 66.63 Mb. The largest contig was approximately 4.71 Mb,
while the N50 was 12,994 bp. This means that half of the total assembled
sequence length was contained in contigs of at least 12,994 bp.

### Assembly Graph

The resulting Flye assembly graph was visualised using Bandage to
examine connections between assembled sequences. The graph contained
several large circular and interconnected structures alongside numerous
smaller fragments, reflecting the complexity of the BBD metagenomic
assembly.

Although some structures appeared circular, their shapes alone do not
confirm that they represent complete bacterial chromosomes.

The assembled contigs were subsequently used for genome binning with
MetaBAT2.

### Processing Log

The sequencing reads were linked to the working directory before running
Flye.

``` bash
cd ~/bbd-metagenome/flye

ln -s /pvol/data/metagenome_assembly/bbd_0.1.fastq.gz .
```

Flye was then submitted to the SLURM scheduler using the following
command:

``` bash
sbatch run_flye_bbd.sh
```

The assembly was performed using the script
[run_flye_bbd.sh](flye/run_flye_bbd.sh).

The main Flye command used in the script was:

``` bash
flye --nano-hq bbd_0.1.fastq.gz --threads 12 --out-dir bbd --meta
```

The `--nano-hq` option specified the nanopore read type, `--threads 12`
allocated 12 processing threads, `--out-dir bbd` specified the output
directory, and `--meta` enabled metagenome assembly mode.

The resulting assembly statistics and graph were examined before
proceeding to genome binning.
