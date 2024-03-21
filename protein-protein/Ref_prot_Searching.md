# Trying to find Ref_protein in our de-novo transcriptome data
Ref_protein - as the ref protein we decided to take the XXXX from YYYYY. Because it's the one, which was detected by our collegues via comparing metagenomics data from metridia londa and clean sea water. 
Also chosing of XXX is based on:
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
___________________________________________________________________________________________
TransDecoder.LongOrfs 5.5.0
___________________________________________________________________________________________
conda create -n hmm
conda activate hmm
conda install -c biocore hmmer
hmmsearch -h
___________________________________________________________________________________________
# hmmsearch :: search profile(s) against a sequence database
# HMMER 3.1b2 (February 2015); http://hmmer.org
Usage: hmmsearch [options] <hmmfile> <seqdb>
```

##### Running for step extract the long open reading frames TransDecoder
```
TransDecoder.LongOrfs -t denovo_transcriptome.fasta
_______________________________________________

```

