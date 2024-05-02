What path we have gone:
1. We had RNA-seq data of unknow species
2. We clean the quality of raw_data and fixed it
3. We assembled RNA-data into de novo transcriptome
4. On this step we will look for possible transcripts and proteins by transdecoder with databases with protein superfamily homology,
then we will align our transcriptome towards protein superfamily database (it will help us to find places, from that we will get protein which is alike one of prtein superfamily)  
5. Then we will look for proteins in transdecoder .pep output file/ We will look for proteins, which have FYY motif anywhere in their sequences.
6. Theese proteins we will blast against refseq protein database to find the best hits.
