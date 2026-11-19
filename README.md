# 23260 Metagenomics Project

This repository contains the code, workflows and notes for the **23260 Metagenomics** course project at DTU.

The project takes raw shotgun metagenomic sequencing reads through a full analysis pipeline, from read quality control to genome-resolved metagenomics.

---

## Project overview

| Week | Step | Tools | Status |
|------|------|-------|--------|
| 2–3 | Read trimming & quality control | BBDuk, FastQC | ✅ |
| … | *Next steps go here* | | ⏳ |

---

## Week 2–3: Trimming and quality control

### Workflow

```
raw FASTQ  ──►  BBDuk  ──►  trimmed FASTQ  ──►  FastQC
```

1. **Raw reads**: paired-end FASTQ files (`*_R1.fastq.gz`, `*_R2.fastq.gz`).
2. **BBDuk**: removes sequencing adapters and trims low-quality bases and short reads.
3. **Trimmed reads**: cleaned FASTQ files used for all downstream analysis.
4. **FastQC**: checks read quality after trimming (per-base quality, adapter content, GC content, length distribution).


**FastQC (quality report)**

```bash
fastqc results/trimmed/*.fastq.gz -o results/fastqc/ -t 8
```

To compare samples, the individual FastQC reports can optionally be combined with MultiQC:

```bash
multiqc results/fastqc/ -o results/multiqc/
```

### Running with Snakemake

The steps are wrapped in a Snakemake workflow (`Snakefile`). Check the workflow first with a dry run:

```bash
snakemake -s Snakefile --dryrun -p
```

Then run it:

```bash
snakemake -s Snakefile --cores 8
```

> On the HPC, load Snakemake first (e.g. `module load snakemake/<version>`), or follow the course's `config.sh`.

---

## Repository structure

```
.
├── Snakefile          # Snakemake workflow
├── config.sh          # Paths / environment setup
├── scripts/           # Helper scripts
├── raw_reads/         # Input FASTQ files (not tracked in git)
├── results/
│   ├── trimmed/       # BBDuk output
│   └── fastqc/        # FastQC reports
└── README.md
```

---

## Requirements

- [BBTools](https://jgi.doe.gov/data-and-tools/software-tools/bbtools/) (BBDuk)
- [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)
- [MultiQC](https://multiqc.info/) (optional)
- [Snakemake](https://snakemake.readthedocs.io/)

---

## Author

**Bhavya Gudhka**, MSc Bioinformatics, DTU
Course: 23260 Metagenomics
