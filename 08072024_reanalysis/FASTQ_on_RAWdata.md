## Quality check and fixing raw data

#### I will use fastQC, multiQC, curadapt or trimmomatic, raw data are in .fastq format. structure of data visualised below

work on organisation server, working folder is: `/mnt/SSD4TB/PROJECTS/kozyreva_works/metridia_master/raw/again`

let's look to the files, before i had had .fastq.tar archive, so `tar -xf P017.fastq.tar` was made.
Now i have got an "analysis/fastq" folder in my working directory, which contains a bunch of .fastq.gz files. 


```
for filename in *.fastq.gz;do gzip -d $filename && echo -e “basename ($filename .fastq.gz).fastq `cat $filename | wc -l | awk ‘{print $1 4}’`”; done
```
