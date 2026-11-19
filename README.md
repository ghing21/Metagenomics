# 23260 Metagenomics Project

This repository contains the code, workflows and notes for the 23260 Metagenomics course project at DTU.

The project takes raw shotgun metagenomic sequencing reads through a full analysis pipeline, from read quality control to genome-resolved metagenomics.

## Project overview

| Week | Step | Tools | Status |
|------|------|-------|--------|
| 2–3 | Read trimming & quality control | BBDuk, FastQC | ✅ |
| 4 | Read-based taxonomic profiling | Kraken, Kraken2, Bracken | ✅ |
| … | Next steps go here | | ⏳ |

## Week 2–3: Trimming and quality control

### Workflow

```
raw FASTQ  ──►  BBDuk  ──►  trimmed FASTQ  ──►  FastQC
```

- **Raw reads**: paired-end FASTQ files (`*_R1.fastq.gz`, `*_R2.fastq.gz`).
- **BBDuk**: removes sequencing adapters and trims low-quality bases and short reads.
- **Trimmed reads**: cleaned FASTQ files used for all downstream analysis.
- **FastQC**: checks read quality after trimming (per-base quality, adapter content, GC content, length distribution).

### FastQC (quality report)

```bash
fastqc results/trimmed/*.fastq.gz -o results/fastqc/ -t 8
```

To compare samples, the individual FastQC reports can optionally be combined with MultiQC:

```bash
multiqc results/fastqc/ -o results/multiqc/
```

## Week 4: Taxonomic profiling

### Workflow

```
trimmed FASTQ  ──►  Kraken (k=31)
               ──►  Kraken2 (k=35)  ──►  Bracken
```

- **Kraken / Kraken2**: classify each read by matching its k-mers against a reference database.
  - `*_kraken.out`: one line per read (classified/unclassified, assigned taxid, k-mer hits).
  - `*_kraken.report`: summary of read counts per taxon across all ranks.
- **Kraken vs Kraken2**: Kraken2 stores minimizers instead of all k-mers, so it is faster and uses less memory. Its longer k-mers give more unclassified reads but more reliable assignments.
- **Bracken**: redistributes reads that Kraken2 left at higher ranks (genus and above) down to species level, giving species-level abundance estimates (`*_bracken.S.report`).

## Running with Snakemake

The steps are wrapped in a Snakemake workflow (`Snakefile`). Check the workflow first with a dry run:

```bash
snakemake -s Snakefile --dryrun -p
```

Then run it:

```bash
snakemake -s Snakefile --cores 8
```

> On the HPC, load Snakemake first (e.g. `module load snakemake/<version>`), or follow the course's `config.sh`.

## Repository structure

```
.
├── Snakefile          # Snakemake workflow
├── config.sh          # Paths / environment setup
├── scripts/           # Helper scripts
├── raw_reads/         # Input FASTQ files (not tracked in git)
├── results/
│   ├── trimmed/       # BBDuk output
│   ├── fastqc/        # FastQC reports
│   ├── kraken/        # Kraken output & reports
│   ├── kraken2/       # Kraken2 output & reports
│   └── bracken/       # Bracken species abundances
└── README.md
```

## Requirements

- [BBTools](https://jgi.doe.gov/data-and-tools/software-tools/bbtools/) (BBDuk)
- [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)
- [MultiQC](https://multiqc.info/) (optional)
- [Kraken](https://ccb.jhu.edu/software/kraken/) / [Kraken2](https://github.com/DerrickWood/kraken2)
- [Bracken](https://github.com/jenniferlu717/Bracken)
- [Snakemake](https://snakemake.readthedocs.io/)
