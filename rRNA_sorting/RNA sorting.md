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
Cycle for all samples:
```
//////
```
