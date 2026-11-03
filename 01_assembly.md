BBD Metagenome: Assembly
================
Rowwyn Lontayao
2026-11-03

<!--
Explain what this step does and why, and copy each command you ran into a
bash chunk. R chunks run from the top of the repository.
-->

## Metagenome Assembly Using Flye

The Black Band Disease (BBD) metagenomic sequencing reads were assembled
using Flye, a genome assembler designed for long-read sequencing data.
The purpose of this step was to combine overlapping nanopore reads into
longer contiguous sequences (contigs), which can be used to investigate
the microbial community associated with BBD.

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
while the N50 was 12,994 bp, indicating that half of the assembled
sequence length was contained in contigs of at least this size.

The resulting assembly graph was visualised using Bandage to examine
connections between assembled sequences. The graph contained several
large connected structures alongside numerous smaller fragments,
illustrating the complexity of the metagenomic assembly.

The assembled contigs were subsequently used for genome binning with
MetaBAT2.

### Commands Used

``` bash
cd ~/bbd-metagenome/flye

ln -s /pvol/data/metagenome_assembly/bbd_0.1.fastq.gz .

sbatch run_flye_bbd.sh
```

``` bash
#!/bin/bash
#SBATCH --time=720
#SBATCH --ntasks=12 --mem=20gb

echo "Starting flye bbd in $(pwd) at $(date)"
flye --nano-hq bbd_0.1.fastq.gz --threads 12 --out-dir bbd --meta
echo "Finished flye bbd in $(pwd) at $(date)"
```
