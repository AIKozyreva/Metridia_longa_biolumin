# looking for rRNA places in transcriptome 
Tools: sortmerna.
## Installing
```
conda create -n sortmerna
conda activate sortmerna
conda install -c bioconda sortmerna
sortmerna -h
```
> Program: SortMeRNA version 4.3.6
>  Usage: sortmerna -ref FILE [-ref FILE] -reads FWD_READS [-reads REV_READS] [OPTIONS]

## Downloading databases for ref
We will get instruction for downloading default ref database set from this of.doc.page (https://sortmerna.readthedocs.io/en/latest/databases.html)
```
mkdir RNAsort
wget https://github.com/biocore/sortmerna/releases/download/v4.3.4/database.tar.gz
tar -xvf database.tar.gz -C RNAsort
```
Now we have a bunch of different databases files 

## Running 
SortMeRNA has a limit on the length of input reads. Each read shouldn't be longer than 33.000 nt. In ourcase we had as input transcriptome.fasta with assembled contigs.
The longest one is 42.000+ nt, that's why we have got an error:
```
sortmerna -ref /mnt/projects/users/aalayeva/RNAsort/smr_v4.3_default_db.fasta -reads /mnt/projects/users/aalayeva/rac/analysis/try1/transcriptome.fasta -workdir /mnt/projects/users/aalayeva/rac/analysis/sortRNA ---fastx --sam --blast 1 --num_alignments 5 
```

> [validate:291] ERROR: Read ID: 0_0 Header: >NODE_1_length_42804_cov_327.435434_g0_i0 Sequence length: 42804 > 30000 nt
>   Please check your reads or contact the authors. Segmentation fault (core dumped).

Fortunately, for checking the amount of rRNA contamination of raw data we don't have to run sortmerna on transcriptome 
(if there were too many rRNA we have got poor quality of transcriptome, while is there wasn't too mich rrna, then we have got pkay-quality transcriptome. 
We will check it by busco next step). Now we can check rRNA amount on raw input .fastq 

Reminder: we have had 7 samples with paired data, so we have S1_R1.fastq+S1_R2.fastq and so on 7 times.  

Command for 1 sample will be:
```
sortmerna -ref /mnt/projects/users/aalayeva/RNAsort/smr_v4.3_default_db.fasta -reads /mnt/projects/users/aalayeva/rac/analysis/try1/S1_R1.fastq -reads /mnt/projects/users/aalayeva/rac/analysis/try1/S1_R2.fastq -workdir /mnt/projects/users/aalayeva/rac/analysis/sortRNA ---fastx --sam --blast 1 --num_alignments 5 
```
Such command takes too much time and for us only log-file with stat.report is enoug, so we will delete -fastx -sam -blast parameters for cycle.

Cycle for all samples:
```
#!/bin/bash

# Define the directory paths
ref="/mnt/projects/users/aalayeva/RNAsort/smr_v4.3_default_db.fasta"
input_dir="/mnt/projects/users/aalayeva/rac/analysis/try1"
output_dir="/mnt/projects/users/aalayeva/rac/analysis/sortRNA"

# Loop through each sample
for ((i=2; i<=7; i++)); do
    sample="S$i"
    workdir="$output_dir/$sample"
    mkdir -p "$workdir"  # Create the output directory if it doesn't exist

    # Run the command for the current sample
    sortmerna -ref "$ref" \
              -reads "$input_dir/${sample}_R1.fastq" \
              -reads "$input_dir/${sample}_R2.fastq" \
              -workdir "$workdir" \
              --num_alignments 5
done
```
Anyway each command is time-consumind due to sortmerna produs many alignments and has blast-like algorithm inside it (as far as i caught), and we know that blast is "long-running" one.

For _1 sample_ we have got _49% of reads were aligned towards rRNA-default database_ from both paired files. So, that's not perfect library for rna-seq due to high level of rRNA data in the raw samples. It might be possible for our collegues to recheck and maybe update their "small RNA filtering" part of extraction and lib-preparing protocol.
