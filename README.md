# variant-annotation
Annotate variants from long read data. 

# Purpose
this workflow processes long-read (oxford nanopore) data, and filters the data, maps it to a reference genome, and generates several alignment file formats.

# Workflow steps

1. Filters using `Filtlong v0.2.1`, keeping 95% of best reads with the flag `-p 95`

2. Maps the filtered data to reference genomes using `minimap2 2.22-r1101`, a mapper for long-read sequences. It uses the flag `-x map-ont`

3. Using samtools `samtools 1.13` to perform a alignment file format conversions.

# Input
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



