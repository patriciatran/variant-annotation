# Long-read Variant Calling Annotation pipeline

# Purpose
The goal of this pipeline is to perform variant calling on long-read (e.g. Oxford Nanopore ONT technologies) sequencing data against a reference bacterial genome of interests. The program generated multiple alignment files in the .SAM and .BAM format. Additionally, it includes two steps to functionally annotate the bacterial genomes using multiple reference genome databases. This can help match in which genes the variants tends to occur.

# Cyberinfrastructure & Implementation

These scripts are meant to be run by HTCondor, a workflow manager that takes in an executable file and a submit file. It is meant to be run on the UW-Madison shared campus-computing infrastructure CHTC, but could work on other systems with few modifications. The pipeline takes advantage of the high-throughput scaling abilities of HTCondor to submit multiple jobs at the same time. For example, if we had an experimental design of 3 treatment, 3 replicate and 3 reference genomes and 10 time points, we would have to perform the steps 3*3*3*10 = 270 times. Instead, we write the metadata (information about the sample design) in a comma separated file containing 270 rows, and HTCondor will submit 270 jobs at the same time for us.

# Workflow steps

![Workflow design](https://github.com/UW-Madison-Bacteriology-Bioinformatics/variant-annotation/blob/main/workflow.png)

1. Filters using `Filtlong v0.2.1`, keeping 95% of best reads with the flag `-p 95`

2. Maps the filtered data to reference genomes using `minimap2 2.22-r1101`, a mapper for long-read sequences. It uses the flag `-x map-ont`

3. Using samtools `samtools 1.13` to perform a alignment file format conversions.

4. Uses `Bakta version 10.0.3` to functionally annotate the reference genomes

5. Uses EGGNOG mapper to annotate the bakta-annotated proteins (`.faa`) into different functional annotations like COG, KEGG, etc.

# Input folder set-up
The folder structure is a main folder, with a subfolder called "reads", and any reference file with the format {REFERENCE}_assembly.fasta

For example:
```
folder
-- reads
---- sample1.fastq.gz
---- sample2.fastq.gz
---- sample3.fastq.gz
reference1_assembly.fasta
reference2_assembly.fasta
reference3_assembly.fasta
```

# Expected output directory structure
The program will create the following files/output, namely 2 folders `filtlong` and `mapping` with all the relevant output files.
```
folder
-- reads
---- sample1.fastq.gz
---- sample2.fastq.gz
---- sample3.fastq.gz
reference1_assembly.fasta
reference2_assembly.fasta
reference3_assembly.fasta
-- filtlong
---- {sample}_filtlong.fastq.gz
-- mapping
---- .sam, .bam, .coverage.tsv, .mapped.bam, .mapped.sorted
```

# Instructions

Log into chtc:
```
ssh [netid]@ap2001.chtc.wisc.edu
# enter password
```

Clone this directory
```
git clone https://github.com/patriciatran/variant-annotation.git
cd variant-annotation
```

Make the scripts executable:
```
chmod +x scripts/*.sh
```

Enter the scripts directory, and create 2 metadata tables (comma-separated values)
The first metadata table (`Samples_and_Ref.txt`) should contain 3 columnes: the sample name, the reference name, and the path to the staging folder containing these files.
The 2nd metadata table (`references.txt`) should contain 2 columns: the reference name, and the path to the staging folder containing these files.

```
cd scripts
nano Samples_and_Ref.txt
# enter your data, quit, save
nano references.txt
# enter your data, quit, save.
```

Submit your htcondor jobs:
```
condor_submit 01_filtlong.sub
```
repeat for 02,03,04 and 05.

## Next steps
This workflow will create large files. I recommend using Globus.org to transfer files to your ResearchDrive or to your personal endpoint.
For instructions, please visit: https://chtc.cs.wisc.edu/uw-research-computing/globus

## References
This pipeline uses the following tools:

- Filtlong: https://github.com/rrwick/Filtlong
- Minimap: https://github.com/lh3/minimap2
- Samtools: https://www.htslib.org/download/
- Bakta: https://github.com/oschwengers/bakta
- Eggnog-mapper: https://github.com/eggnogdb/eggnog-mapper

## Citation
If you find this pipeline helpful, please consider citing this repository.


