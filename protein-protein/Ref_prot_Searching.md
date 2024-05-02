# Trying to find Ref_protein in our de-novo transcriptome data
Ref_protein - as the ref protein we decided to take the _Isopenicillin N synthase_ (IPNS_STRCL; interpro - P10621; conserv-e site - IPR002057) from _Streptomyces clavuligerus_ gene _pcbC_. Because it's the one, which was detected by our collegues via comparing metagenomics data from metridia londa and clean sea water. 
Also chosing of IPNS_STRCL is based on:
> Были выдвинуты гипотезы (Oba et al., 2009) о биосинтезе целентеразина:
> механизм нерибосомального синтеза циклизация и дальнейшая модификация аминокислотных остатков FYY,  как части более длинного пептида
> 
> Поиск пептидов с мотивом FYY обнаружил наличие гомологов оксигеназ и изопенициллин-N-синтаз у биолюм. гребневиков и не обнаружил у небиолюм. (Francis et al., 2015)

### Step 1. Downloading and preparing data
We can download **protein-superfamily.fasta** with all sequences, which matching the Isopenicillin protein superfamily, from InterPro database, which contain pFam database for today. 
And also we have to download **Pfam-a.hmm.gz** from the same InterPro. 
_https://www.ebi.ac.uk/interpro/entry/InterPro/IPR027443/protein/reviewed/#table_ - we will need them later and also we need them to prepare better searching for potential protein coding sites from our transcriptome secuence.
_https://www.ebi.ac.uk/interpro/download/Pfam/_ - Pfam-A HMMs in an HMM library searchable with the hmmscan program. we need them for better ORF finding and better coding sequences prediction, based on hmm and pfam made homology.

### Step 2. De-novo transcriptome into protein sequence (ORF finding and coding sequences prediction based on homolody, which we have got by hmmer and pfam data).
Tool: TransDecoder, hmmsearch

##### Installing
```
conda create -n transdecoder
conda activate transdecoder
conda install -c bioconda transdecoder
/path to/TransDecoder.LongOrfs --version
```
TransDecoder.LongOrfs 5.5.0

```
conda create -n hmm
conda activate hmm
conda install -c biocore hmmer
hmmsearch -h
```
hmmsearch :: search profile(s) against a sequence database
HMMER 3.1b2 (February 2015); http://hmmer.org
Usage: hmmsearch [options] <hmmfile> <seqdb>

##### Running for step extract the long open reading frames TransDecoder
```
TransDecoder.LongOrfs -t soft_filtered_transcripts.fasta
_______________________________________________
we got /.../soft_filtered_transcripts.fasta.transdecoder_dir with bunch of files, including **longest_orfs.pep**
```
##### Make additional steps with making homology dataset from pfam and hmmer
```
gzip -d /mnt/projects/users/aalayeva/rac/Pfam-A.hmm.gz
hmmsearch --cpu 8 -E 1e-10 --domtblout pfam.domtblout /../rac/Pfam-A.hmm /../analysis/soft_filtered_transcripts.fasta.transdecoder_dir/longest_orfs.pep
_______________________________________________
we got pfam.domtblout non epty file in the current location
```

##### Running the second Prediction transdecoder step
Here i have to note, for running you have to be in the dir, where /soft_filtered_transcripts.fasta.transdecoder_dir from first step is located. And one more: all sequences with non atgc alphabet will be skipped by the program with _"Error, feature_seq: NNNNNNNNNNNNNNNNNNNAATGTGCAGTATTG contains non-GATC chars... skipping"_ mark. In the end we should get a bunch of files in our current location: .bed, .gff3, .pep, .cds; 

how to read the output: https://github.com/TransDecoder/TransDecoder/wiki#running-transdecoder 
```
TransDecoder.Predict -t /../analysis/soft_filtered_transcripts.fasta --retain_pfam_hits /../analysis/pfam.domtblout
```
##### Search for peptides with an FYY motif**

First we found the sections that contain the FYY motif and then picked the ones that end with FYY (cases of losing stop-codon, for example because of degradation).
```
awk 'BEGIN{RS=">";FS="\n"} {seq=$2; gsub("\n","",$2); if(index(seq, "FYY") || index(seq, "YYF")) {print ">"$1"\n"$2"\n"}}' longest_orfs.pep > filtered_hypothetical_peptides.fasta

grep -B 1 "FYY$" filtered_hypothetical_peptides.fasta > FYYend_hypothetical_peptides.fasta
```
Also created a file that contains sections with the FYY motif before the stop codon (normal cases with FYY motif on the protein end).
```
grep -B 1 --no-group-separator  "FYY\*" longest_orfs.pep >fyy_peptides.fasta
```
Then merged two file t0 get one common file with proteins, which have FYY anywhere in the sequence:
```
cat fyy_peptides.fasta FYYend_hypothetical_peptides.fasta > all_fyy.fasta
```

##### BLAST the results FYY-proteins file**

As reference database we decided to take refseq protein, because it contains only reviewed proteins, which sequence or properties were confirmed.

```
update_blastdb.pl --decompress  "refseq_protein" --force --verbose #download db

blastp -db /local/workdir/dmm2017/blast_protein/refseq_protein -query all_fyy.fasta -out blast_results/blastp_results.txt -evalue 0.001 -outfmt "6 qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore stitle" -num_threads 4 #look for hits in our common file towards refseq db
```

##### Differential expression analysis

**1. Mapping reads to assembled transcriptome**
It was decided to run mapping in two ways: separately for each sample and also run for groups of luminous (2,4,6) and non-luminous parts (3,5,7).

Examples of running mapping using hisat2:
```
hisat2-build -p 16 soft_filtered_transcripts.fasta tr_index

hisat2 -x tr_index -1 S1_R1.fastq.gz -2 S1_R2.fastq.gz | samtools view -Su - | samtools sort --threads 30 -m 12G -o metridia.1.hisat.bam -
```
```
hisat2 -x tr_index -1 S2_R1.fastq.gz,S4_R1.fastq.gz,S6_R1.fastq.gz -2 S2_R2.fastq.gz,S4_R2.fastq.gz,S6_R2.fastq.gz | samtools view -Su - | samtools sort --threads 30 -m 12G -o metridia.246.hisat.bam -

hisat2 -x tr_index -1 S3_R1.fastq.gz,S5_R1.fastq.gz,S7_R1.fastq.gz -2 S3_R2.fastq.gz,S5_R2.fastq.gz,S7_R2.fastq.gz | samtools view -Su - | samtools sort --threads 30 -m 12G -o metridia.357.hisat.bam -
```

**2. Calculate Counts table**

Calculating transcript abundance, using FeatureCounts and then performing differential expression using DESeq2.
